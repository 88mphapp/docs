# 智能合约索引

智能合约源代码均可在 [GitHub](https://github.com/88mphapp/88mph-contracts)找到。

## 体系概览

每个88mph的流动池子都由六个智能合约搭建而成。

- `DInterest`: 用户主要参与互动的条约，功能包括处理存取款信息、跟踪记录存款及发射子节点所使用的所有事件。
- `FeeModel`: 决定88mph的社区服务费以及其获得者。
- `MoneyMarket`:与其他收益平台互动的包装，储存所有用户资金，一个条约仅适用于与一个平台互动。
- `InterestOracle`: 预计基础收益平台利率的变化。
- `NFT`: 根据ERC721标准发行的两种NFT，是存款或债券所有人的持仓凭证。

在全球范围内，88mph用以下条约来处理与MPH代币相关的事务。

- `MPHToken`:MPH ERC-20代币合约。 
- `MPHMinter`:负责铸币，从储户和债券购买者处收回MPH代币。
- `Dumper`: 负责累加社区服务费及在88mph流动池子里产生的挖矿收益并将收益换成DAI。
- `Rewards`:MPH锁仓挖矿奖励合约，负责分配社区服务费和挖矿奖励。

## 应用程序接口索引

### DInterest

#### 状态变换函数

##### `function deposit(uint256 amount, uint256 maturationTimestamp) external`

负责为来访者建立一笔单一存款。

- `amount`: 存入的`stablecoin`数量。在调用此函数前，来访者应该已经认可了支出这么多`stablecoin`（稳定币）的合同。计算公式为\(10^{stablecoinDecimals}\)。
- `maturationTimestamp`: Unix时间戳，数秒记录存款时间，失效即期满。

##### `function withdraw(uint256 depositID) external`

负责为来访者取出一笔单一存款。来访者必须拥有ID为`depositID`存款的NFT凭证。

- `depositID`: `deposits`数组中要提取的存款的指数加1。
- `fundingID`: 在`fundingList`数组中为利息赤字供资的债券买家的指数加1。可以使用我们的[subgraph](https://thegraph.com/explorer/subgraph/bacon-labs/eighty-eight-mph)找到该ID。

###### 注意事项

如果88mph流动池子在基础交易市场中无法产生足额利息来支付承诺给用户的利息，并且也没人买债券来清偿赤字，那么该取款则有可能失败。这是在88mph平台上投资的主要风险。

##### `function earlyWithdraw(uint256 depositID) external`

负责未到期的提前取款事宜。来访者必须拥有ID为`depositID`的存款的NFT凭证。

- `depositID`: `deposits`数组中要提取的存款的指数加1。
- `fundingID`: 在`fundingList`数组中为利息赤字供资的债券买家的指数加1。可以使用我们的[subgraph](https://thegraph.com/explorer/subgraph/bacon-labs/eighty-eight-mph)找到该ID。

##### `function multiDeposit(uint256[] calldata amountList, uint256[] calldata maturationTimestampList) external`

负责为来访者存入多笔存款。每个数组中每个索引的值将被合并以创建单个存款。

- `amountList`：要存入的`stablecoin`数量的数组。在调用此函数前，来访者应该已经认可了支出这么多`stablecoin`的合同。计算公式为\(10^{stablecoinDecimals}\)。
- `maturationTimestampList`:Unix时间戳数组，记录存款时间，以秒为单位，失效即期满。

###### 输入大小限制

建议的最大存款数为20。

##### `function multiWithdraw(uint256[] calldata depositIDList) external`

负责为来访者取出多笔存款。来访者必须拥有ID为`depositIDList`中的存款NFT。

- `depositIDList`：`deposits`数组中要提取的各笔存款的索引加1。
- `fundingID`: 在`fundingList`数组中为利息赤字供资的债券买家的索引加1。可以使用我们的[subgraph](https://thegraph.com/explorer/subgraph/bacon-labs/eighty-eight-mph)找到该ID。

###### 输入大小限制

建议的最大存款数为100。

###### 注意事项

如果88mph流动池在基础收益市场中无法产生足额利息来偿算承诺给用户的利息，并且也没人买债券来补足差额，那么取款则有可能失败。这是在88mph平台上投资的主要风险。如果取款失败，`earlyWithdraw()`程序可能被唤醒从本金中扣除前一阶段预付的利息和费用。

##### `function multiEarlyWithdraw(uint256[] calldata depositIDList) external`

负责在存期未满时为来访者取出多笔存款。来访者必须拥有ID为`depositIDList`中的存款NFT。

- `depositIDList`: `deposits`数组中要提取的各笔存款的索引加1。
- `fundingIDList`: 在`fundingList`数组中为多笔利息赤字供资的债券买家的索引加1。可以使用我们的[subgraph](https://thegraph.com/explorer/subgraph/bacon-labs/eighty-eight-mph)找到ID。

###### 输入大小限制

建议的最大存款数为50。

##### `function fundAll() external`

允许来访者为88mph池中的所有现有赤字提供资金。作为交换，来访者会收到一笔NFT资金，只要来访者所资助的存款被储户取出，该笔资助就会填补利息赤字发放给相应的储户。

在调用此函数之前，来访者必须至少批准`stablecoin`的赤字金额到`DInterest`合约。 可以使用[`surplus()`](#function-surplus-external-view-returns-bool-isnegative-uint256-surplusamount)获得此金额。为避免交易被还原，建议您将批准金额简单地设置为\(2^{256}-1\)。

##### `function fundMultiple(uint256 toDepositID) external`

允许来访者为多笔存款的赤字提供资金。作为交换，来访者会收到一笔NFT资金，只要来访者所资助的存款被储户取出，该笔资助就会填补利息赤字发放给相应的储户。

在调用此函数之前，来访者必须至少批准`stablecoin`的赤字金额到`DInterest`合约。 可以使用 [`surplusOfDeposit()`](#function-surplusofdeposituint256-depositid-external-view-returns-bool-isnegative-uint256-surplusamount)获得此金额。为避免交易被还原，建议您将批准金额简单地设置为\(2^{256}-1\)。

- `toDepositID`: ID从（不包括） `lastFundedDepositID`到（包括）`toDepositID`的存款将会得到资助。

#### 只读函数

##### `function getDeposit(uint256 depositID) external view returns (uint256 amount, uint256 maturationTimestamp, uint256 initialDeficit, uint256 initialMoneyMarketIncomeIndex, bool active)`

负责返回有关用户存款的信息。存款所有者是拥有ID为`depositID`的ERC721存款代币的以太坊账户。

###### 输入值

- `depositID`:`deposits`数组中的存款索引。

###### 返回值

- `amount`:存入的稳定币数量。计算公式为\(10^{stablecoinDecimals}\)。
- `maturationTimestamp`: Unix时间戳，记录存款时间，以秒为单位，失效即期满。
- `interestOwed`: 承诺该存款的初始利息。
- `initialMoneyMarketIncomeIndex`: 在存期内由[`moneyMarket.incomeIndex()`](#function-incomeIndex-external-returns-uint256) 返回的实际值。
- `active`: 如未取存款则为`true`, 反之则为`false`.
- `finalSurplusIsNegative`:如果提款时存款有负债则为 `true`，反之则为`false`。
- `finalSurplusAmount`: 提款时存款的负债金额。
- `mintMPHAmount`: 存款时为用户铸造的MPH令牌数量。

##### `function getFunding(uint256 fundingID) external view returns (uint256 fromDepositID, uint256 toDepositID, uint256 recordedFundedDepositAmount, uint256 recordedMoneyMarketIncomeIndex)`

返回有关债券的信息。债券所有者（以及将获得利息的帐户）是拥有ID为`fundingID`的ERC721债券代币的以太坊帐户。

###### 输入值

- `fundingID`：`fundingList`数组中的债券索引。

###### 返回值

- `fromDepositID`: ID从（不包括）`fromDepositID`到（包括）`toDepositID`的利息赤字由该债券提供资金。
- `toDepositID`: ID从（不包括）`fromDepositID`到（包括）`toDepositID`的利息赤字由该债券提供资金。
- `recordedFundedDepositAmount`: 该债券所有者所资助利息的总存款数目，以稳定币表示。 计算公式为\（10 ^ {stablecoinDecimals} \）。
- `recordedMoneyMarketIncomeIndex`:在该债券所资助的存款最近一次被提取时，[`moneyMarket.incomeIndex（）`]（#function-incomeIndex-external-returns-uint256）返回的值。如果尚未提取任何存款，则该值等于购买债券时的货币市场收入指数。

##### `function MinDepositPeriod() external view returns (uint256)`

返回最短存款期限，以秒为单位。

##### `function MaxDepositPeriod() external view returns (uint256)`

返回最长存款期限，以秒为单位。

##### `function MinDepositAmount() external view returns (uint256)`

返回单笔`stablecoin`（稳定币）存款的最小存款额。 计算公式为\（10 ^ {stablecoinDecimals} \）。

##### `function MaxDepositAmount() external view returns (uint256)`

返回单笔`stablecoin`（稳定币）存款的最大存款额。 计算公式为\（10 ^ {stablecoinDecimals} \）。

##### `function totalDeposit() external view returns (uint256)`

返回`stablecoin`的总存款金额。 计算公式为\（10 ^ {stablecoinDecimals} \）。

##### `function moneyMarket() external view returns (address)`

返回`MoneyMarket`的地址。

##### `function stablecoin() external view returns (address)`

返回所用稳定币的地址。

##### `function feeModel() external view returns (address)`

返回`FeeModel`的地址。

##### `function interestModel() external view returns (address)`

返回`InterestModel`的地址。

##### `function interestOracle() external view returns (address)`

返回`InterestOracle`的地址。

##### `function depositNFT() external view returns (address)`

返回ERC721存款代币的地址。

##### `function fundingNFT() external view returns (address)`

返回ERC721债券代币的地址。

##### `function calculateInterestAmount(uint256 depositAmount, uint256 depositPeriodInSeconds) external view returns (uint256 interestAmount)`

返回给定存款金额和期限的利息金额。

###### 输入值

- `depositAmount`:`stablecoin`存款金额。计算公式为\(10^{stablecoinDecimals}\)。
- `depositPeriodInSeconds`: 存款时长，以秒为单位。

##### `function surplus() external view returns (bool isNegative, uint256 surplusAmount)`

返回池中现有存款数的利息。

###### 返回值

- `isNegative`: 盈余是否为负。 盈余为负表示存在赤字。
- `surplusAmount`: 池中现有存款的盈余数，以稳定币表示。计算公式为\(10^{stablecoinDecimals}\)。

##### `function surplusOfDeposit(uint256 depositID) external view returns (bool isNegative, uint256 surplusAmount)`

返回某笔特定存款的盈余数目。不包括债券。

###### 输入值

- `depositID`:要提取的存款索引，在 `deposits`数组中加1。

###### 返回值

- `isNegative`: 盈余是否为负。 盈余为负表示存在赤字。
- `surplusAmount`: 池中现有存款的盈余数，以稳定币表示。计算公式为\(10^{stablecoinDecimals}\)。

##### `function depositsLength() external view returns (uint256)`

返回`deposits`数组的长度。

##### `function fundingListLength() external view returns (address)`

返回`fundingList`数组的长度。

##### `function depositIsFunded(uint256 depositID) external view returns (bool)`

###### 输入值

- `depositID`:要提取的存款索引，在`deposits`数组中加1。

###### 返回值

返回是否已为存在赤字的存款提供资金。

##### `function latestFundedDepositID() external view returns (uint256)`

返回资金存款中的最大ID值。 可以假设所有ID小于或等于此值的存款都已被资助。

##### `function unfundedUserDepositAmount() external view returns (uint256)`

返回未填补赤字的`stablecoin`（稳定币）存款金额。计算公式为\（10 ^ {stablecoinDecimals} \）。

### 费用模型（FeeModel）

#### 只读函数

##### `function getFee(uint256 _txAmount) external pure returns (uint256 _feeAmount)`

用于确定从交易中收取多少社区服务费。

###### 输入值

- `_txAmount`: 待收费的该笔利息金额。

###### 返回值

- `_feeAmount`: 从交易中收取的费用金额。

##### `function beneficiary() external view returns (address)`

返回将收取费用的地址。

### 货币市场

### 状态变化函数

##### `function deposit(uint256 amount) external`

放入基础货币市场的稳定币`amount`（数量）。

###### 输入值

- `amount`:要存入的稳定币数量。

##### `function withdraw(uint256 amountInUnderlying) external`

从基础货币市场中提取`amountInUnderlying`稳定币。

###### 输入值

- `amountInUnderlying`:要提取的稳定币数量。

#### 只读函数

##### `function totalValue() external returns (uint256)`

返回货币市场的现有稳定币总值。

##### `function incomeIndex() external returns (uint256)`

返回一个指数，该指数可用于计算一段时间内货币市场产生的利息。具体来说，计算公式为\（interestOverPeriod = depositValueAtBeginningOfPeriod \ times \ frac {incomeIndexAtEndOfPeriod} {incomeIndexAtBeginningOfPeriod} \）。
