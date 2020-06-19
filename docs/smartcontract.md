# Smart contract reference

The smart contract source code can be found on [GitHub](https://github.com/Bacon-labs/88mph-contracts).

## Mainnet deployments

### Aave Pool

- `DInterest`: [0xdf907B483C7e7402555BCb1D8d3878AC3a38F07B](https://etherscan.io/address/0xdf907B483C7e7402555BCb1D8d3878AC3a38F07B)
- `FeeModel`: [0xf1409a2f1F5f53e46BbAfd334311c80e675a410D](https://etherscan.io/address/0xf1409a2f1F5f53e46BbAfd334311c80e675a410D)
- `AaveMarket`: [0x5e53247b147D57fAf9825DAbe6e06AE90Bd70BA9](https://etherscan.io/address/0x5e53247b147D57fAf9825DAbe6e06AE90Bd70BA9)

### Compound Pool

- `DInterest`: [0xeBcE73ED303eB97fA8060F276083444B9bBe63c1](https://etherscan.io/address/0xeBcE73ED303eB97fA8060F276083444B9bBe63c1)
- `FeeModel`: [0xf1409a2f1F5f53e46BbAfd334311c80e675a410D](https://etherscan.io/address/0xf1409a2f1F5f53e46BbAfd334311c80e675a410D)
- `CompoundERC20Market`: [0xEa7827E66bd41aA0F57557CE3516644dbFb11eaF](https://etherscan.io/address/0xEa7827E66bd41aA0F57557CE3516644dbFb11eaF)

### Compound Pool (Legacy)

- `DInterest`: [0x9b226970cdeAdA0026aEd50D02E4A0dD37C92b6F](https://etherscan.io/address/0x9b226970cdeAdA0026aEd50D02E4A0dD37C92b6F)
- `FeeModel`: [0xf1409a2f1F5f53e46BbAfd334311c80e675a410D](https://etherscan.io/address/0xf1409a2f1F5f53e46BbAfd334311c80e675a410D)
- `CompoundERC20Market`: [0xe7326dc7D136c6dF7A4056679A82aBd144631043](https://etherscan.io/address/0xe7326dc7D136c6dF7A4056679A82aBd144631043)

## Architecture overview

88mph consists of 5 smart contracts:

- `DInterest`: The main smart contract that users interact with. Handles depositing, withdrawing, and keeping track of deposits. Emits all of the events used in the subgraph.
- `FeeModel`: Determines the fee strategy of 88mph, as well as who receives the fees.
- `MoneyMarket`: A wrapper for interacting with money market protocols, and stores all of the user funds. Requires a different one for each money market protocol. As of now we have `CompoundERC20Market` and `AaveMarket` written.
- `NFT`: Two non-fungible ERC721 tokens, used for representing ownership of deposits and fundings.

## API reference

### DInterest

#### State changing functions

##### `function deposit(uint256 amount, uint256 maturationTimestamp) external`

Creates a single deposit for the caller.

- `amount`: The amount of `stablecoin` to deposit. The caller should have already approved the contract to spend this much `stablecoin` before calling this function. Scaled by \(10^{stablecoinDecimals}\).
- `maturationTimestamp`: The Unix timestamp at and after which the deposit will be able to be withdrawn. In seconds.

##### `function withdraw(uint256 depositID) external`

Withdraws a single deposit for the caller. The caller must own the deposit NFT with ID `depositID`.

- `depositID`: The index of the deposit to be withdrawn in the `deposits` array plus 1.

###### Important note

Withdrawing may fail if the 88mph pool has failed to generate enough interest from the underlying money market to cover the deficit incurred by the original upfront interest payout, and no one has funded the difference. This is the main risk of depositing into 88mph. If withdrawing actually fails, `earlyWithdraw()` may be called to get back the deposit minus the upfront interest and fee.

##### `function earlyWithdraw(uint256 depositID) external`

Withdraws a single deposit for the caller, before the maturation timestamp. The caller must own the deposit NFT with ID `depositID`.

- `depositID`: The index of the deposit to be withdrawn in the `deposits` array plus 1.

##### `function multiDeposit(uint256[] calldata amountList, uint256[] calldata maturationTimestampList) external`

Deposits multiple deposits for the caller. The values at each index in each array will be combined to create a single deposit.

- `amountList`: An array of the amounts of `stablecoin` to deposit. The caller should have already approved the contract to spend this much `stablecoin` before calling this function. Scaled by \(10^{stablecoinDecimals}\).
- `maturationTimestampList`: An array of the Unix timestamps at and after which the deposits will be able to be withdrawn. In seconds.

##### `function multiWithdraw(uint256[] calldata depositIDList) external`

Withdraws multiple deposits for the caller. The caller must own the deposit NFTs with IDs in `depositIDList`.

- `depositIDList`: The indices of the deposits to be withdrawn in the `deposits` array plus 1.

###### Important note

Withdrawing may fail if the 88mph pool has failed to generate enough interest from the underlying money market to cover the deficit incurred by the original upfront interest payout, and no one has funded the difference. This is the main risk of depositing into 88mph. If withdrawing actually fails, `earlyWithdraw()` may be called to get back the deposit minus the upfront interest and fee.

##### `function multiEarlyWithdraw(uint256[] calldata depositIDList) external`

Withdraws multiple deposits for the caller, before the maturation timestamp. The caller must own the deposit NFTs with IDs in `depositIDList`.

- `depositIDList`: The indices of the deposits to be withdrawn in the `deposits` array plus 1.

##### `function fundAll() external`

Allows the caller to fund all of the existing deficit of the 88mph pool. In exchange, the caller receives a funding NFT, and the interest generated by the deposits the caller funded will be sent to the owner of the NFT whenever a deposit they funded is withdrawn.

Before calling this function, the caller must approve at least the deficit amount of `stablecoin` to the `DInterest` contract. This amount may be obtained using [`surplus()`](#function-surplus-external-view-returns-bool-isnegative-uint256-surplusamount). To avoid the transaction being reverted, it is recommended to simply set the approval amount to \(2^{256}-1\).

##### `function fundMultiple(uint256 toDepositID) external`

Allows the caller to fund the deficit of multiple deposits. In exchange, the caller receives a funding NFT, and the interest generated by the deposits the caller funded will be sent to the owner of the NFT whenever a deposit they funded is withdrawn.

Before calling this function, the caller must approve at least the deficit amount of `stablecoin` to the `DInterest` contract. This amount may be obtained using [`surplusOfDeposit()`](#function-surplusofdeposituint256-depositid-external-view-returns-bool-isnegative-uint256-surplusamount). To avoid the transaction being reverted, it is recommended to simply set the approval amount to \(2^{256}-1\).

- `toDepositID`: Deposits with ID from (not including) `lastFundedDepositID` to (including) `toDepositID` will be funded.

#### Read only functions

##### `function getDeposit(uint256 depositID) external view returns (uint256 amount, uint256 maturationTimestamp, uint256 initialDeficit, uint256 initialMoneyMarketPrice, bool active)`

Returns info about a user deposit. The owner of the deposit is whichever Ethereum account that owns the ERC721 deposit token with id `depositID`.

###### Inputs

- `depositID`: The index of the deposit in the `deposits` array.

###### Returns

- `amount`: The amount of the deposit, in stablecoins. Scaled by \(10^{stablecoinDecimals}\).
- `maturationTimestamp`: The Unix timestamp at and after which the deposit will be able to be withdrawn. In seconds.
- `initialDeficit`: The initial deficit caused by the deposit. Equals to the upfront interest paid plus the fee.
- `initialMoneyMarketPrice`: The value returned by [`moneyMarket.price()`](#function-price-external-view-returns-uint256) at the time of deposit.
- `active`: `true` if the deposit hasn't been withdrawn, `false` otherwise.

##### `function getFunding(uint256 fundingID) external view returns (uint256 fromDepositID, uint256 toDepositID, uint256 recordedFundedDepositAmount, uint256 recordedMoneyMarketPrice)`

Returns info about a funding. The owner of the funding (and the account who will receive the interests) is whichever Ethereum account that owns the ERC721 funding token with id `fundingID`.

###### Inputs

- `fundingID`: The index of the funding in the `fundingList` array.

###### Returns

- `fromDepositID`: Deposits with ID from (not including) `fromDepositID` to (including) `toDepositID` have their deficits funded by this funding instance.
- `toDepositID`: Deposits with ID from (not including) `fromDepositID` to (including) `toDepositID` have their deficits funded by this funding instance.
- `recordedFundedDepositAmount`: The current total deposit amount that is generating interest for the owner of this funding instance, in stablecoins. Scaled by \(10^{stablecoinDecimals}\).
- `recordedMoneyMarketPrice`: The value returned by [`moneyMarket.price()`](#function-price-external-view-returns-uint256) at the time of the latest withdrawal of a deposit funded by this funding instance. If no funded deposit has been withdrawn yet, this value is equal to the monet market price at the time of the funding instance's creation.

##### `function UIRMultiplier() external view returns (uint256)`

Returns the multiplier \(m\) used [here](howitworks.md#technical-details). Scaled by \(10^{18}\).

##### `function MinDepositPeriod() external view returns (uint256)`

Returns the minimum deposit period, in seconds.

##### `function MaxDepositAmount() external view returns (uint256)`

Returns the maximum deposit amount for a single deposit in `stablecoin`. Scaled by \(10^{stablecoinDecimals}\).

##### `function totalDeposit() external view returns (uint256)`

Returns the total deposited amount of `stablecoin`. Scaled by \(10^{stablecoinDecimals}\).

##### `function moneyMarket() external view returns (address)`

Returns the address of `MoneyMarket`.

##### `function stablecoin() external view returns (address)`

Returns the address of the stablecoin used.

##### `function feeModel() external view returns (address)`

Returns the address of `FeeModel`.

##### `function depositNFT() external view returns (address)`

Returns the address of the ERC721 deposit token.

##### `function fundingNFT() external view returns (address)`

Returns the address of ERC721 funding token.

##### `function calculateUpfrontInterestRate(uint256 depositPeriodInSeconds) external view returns (uint256 upfrontInterestRate)`

Returns the current upfront interest rate \(\gamma\) used [here](howitworks.md#technical-details).

###### Inputs

- `depositPeriodInSeconds`: The length of the deposit's deposit period, in seconds.

##### `function blocktime() external view returns (uint256)`

Returns the average block time 88mph uses in its upfront interest rate calculation, in seconds. Scaled by \(10^{18}\).

##### `function surplus() external view returns (bool isNegative, uint256 surplusAmount)`

Returns the surplus value of the pool over the owed deposits.

###### Returns

- `isNegative`: Whether the surplus is negative. A negative surplus means there's a deficit.
- `surplusAmount`: Amount of the pool's surplus, in stablecoins. Scaled by \(10^{stablecoinDecimals}\).

##### `function surplusOfDeposit(uint256 depositID) external view returns (bool isNegative, uint256 surplusAmount)`

Returns the surplus value of a particular deposit. Does not include funding.

###### Inputs

- `depositID`: The index of the deposit to be withdrawn in the `deposits` array plus 1.

###### Returns

- `isNegative`: Whether the surplus is negative. A negative surplus means there's a deficit.
- `surplusAmount`: Amount of the pool's surplus, in stablecoins. Scaled by \(10^{stablecoinDecimals}\).

##### `function depositsLength() external view returns (uint256)`

Returns the length of the `deposits` array.

##### `function fundingListLength() external view returns (address)`

Returns the length of the `fundingList` array.

##### `function depositIsFunded(uint256 depositID) external view returns (bool)`

###### Inputs

- `depositID`: The index of the deposit to be withdrawn in the `deposits` array plus 1.

###### Returns

Returns whether or not the deposit's deficit has been funded.

##### `function latestFundedDepositID() external view returns (uint256)`

Returns the maximum ID among funded deposits. It can be assumed that all deposits with ID less than or equal to this value have been funded.

##### `function unfundedUserDepositAmount() external view returns (uint256)`

Returns the deposited `stablecoin` amount whose deficit hasn't been funded. Scaled by \(10^{stablecoinDecimals}\).

### FeeModel

#### Read only functions

##### `function getFee(uint256 _txAmount) external pure returns (uint256 _feeAmount)`

Used for determining how much fee to charge from a transaction.

###### Inputs

- `_txAmount`: The amount of the transaction from which a fee will be taken.

###### Returns

- `_feeAmount`: The amount of the fee that will be taken from the transaction.

##### `function beneficiary() external view returns (address)`

Returns the address who will receive the fees.

### MoneyMarket

#### State changing functions

##### `function deposit(uint256 amount) external`

Lends `amount` stablecoins to the underlying money market protocol.

###### Inputs

- `amount`: The amount of stablecoins to be deposited.

##### `function withdraw(uint256 amountInUnderlying) external`

Withdraws `amountInUnderlying` stablecoins from the underlying money market protocol.

###### Inputs

- `amountInUnderlying`: The amount of stablecoins to be withdrawn.

#### Read only functions

##### `function supplyRatePerSecond(uint256 blocktime) external view returns (uint256)`

Returns the current interest rate offered by the money market, per second. Scaled by \(10^{18}\).

###### Inputs

- `blocktime`: The average block time, in seconds. Scaled by \(10^{18}\).

##### `function totalValue() external returns (uint256)`

Returns the total value locked in the money market, in terms of the underlying stablecoin. Scaled by \(10^{stablecoinDecimals}\).

##### `function price() external returns (uint256)`

Returns an index that can be used to compute the interest generated by the money market over a period of time. Specifically, \(interestOverPeriod = depositValueAtBeginningOfPeriod \times \frac{priceAtEndOfPeriod}{priceAtBeginningOfPeriod}\).