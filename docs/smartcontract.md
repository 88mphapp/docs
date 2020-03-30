# Smart contract reference

The smart contract source code can be found on [GitHub](https://github.com/Bacon-labs/88mph-contracts).

## Mainnet deployments

- `DInterest`: [0x9b226970cdeAdA0026aEd50D02E4A0dD37C92b6F](https://etherscan.io/address/0x9b226970cdeAdA0026aEd50D02E4A0dD37C92b6F)
- `FeeModel`: [0xf1409a2f1F5f53e46BbAfd334311c80e675a410D](https://etherscan.io/address/0xf1409a2f1F5f53e46BbAfd334311c80e675a410D)
- `CompoundERC20Market`: [0xe7326dc7D136c6dF7A4056679A82aBd144631043](https://etherscan.io/address/0xe7326dc7D136c6dF7A4056679A82aBd144631043)

## Architecture overview

88mph consists of 3 smart contracts:

- `DInterest`: The main smart contract that users interact with. Handles depositing, withdrawing, and keeping track of deposits. Emits all of the events used in the subgraph.
- `FeeModel`: Determines the fee strategy of 88mph, as well as who receives the fees.
- `MoneyMarket`: A wrapper for interacting with money market protocols, and stores all of the user funds. Requires a different one for each money market protocol. As of now we have `CompoundERC20Market` and `AaveMarket` written.

## API reference

### DInterest

#### State changing functions

##### `function deposit(uint256 amount, uint256 maturationTimestamp) external`

Creates a single deposit for the caller.

- `amount`: The amount of Dai to deposit. The caller should have already approved the contract to spend this much Dai before calling this function. Scaled by \(10^{18}\).
- `maturationTimestamp`: The Unix timestamp at and after which the deposit will be able to be withdrawn. In seconds.

##### `function withdraw(uint256 depositID) external`

Withdraws a single deposit for the caller.

- `depositID`: The index of the deposit to be withdrawn in the `userDeposits` array.

##### `function multiDeposit(uint256[] calldata amountList, uint256[] calldata maturationTimestampList) external`

Deposits multiple deposits for the caller. The values at each index in each array will be combined to create a single deposit.

- `amountList`: An array of the amounts of Dai to deposit. The caller should have already approved the contract to spend this much Dai before calling this function. Scaled by \(10^{18}\).
- `maturationTimestampList`: An array of the Unix timestamps at and after which the deposits will be able to be withdrawn. In seconds.

##### `function multiWithdraw(uint256[] calldata depositIDList) external`

Withdraws multiple deposits for the caller.

- `depositIDList`: The indices of the deposits to be withdrawn in the `userDeposits` array.

##### `function sponsorDeposit(uint256 amount, uint256 maturationTimestamp, string calldata data) external`

Creates a single sponsor deposit for the caller.

- `amount`: The amount of Dai to deposit. The caller should have already approved the contract to spend this much Dai before calling this function. Scaled by \(10^{18}\).
- `maturationTimestamp`: The Unix timestamp at and after which the deposit will be able to be withdrawn. In seconds.
- `data`: An arbitrary string that contains metadata about the sponsor. Will be emitted as part of an event, but won't be put in storage.

##### `function sponsorWithdraw(uint256 depositID) external`

Withdraws a single sponsor deposit for the caller.

- `depositID`: The index of the deposit to be withdrawn in the `sponsorDeposits` array.

#### Read only functions

##### `function userDeposits(address user, uint256 depositID) external view returns (uint256 amount, uint256 maturationTimestamp, bool active)`

Returns info about a user deposit.

###### Inputs

- `user`: The address of the user.
- `depositID`: The index of the deposit in the `userDeposits` array.

###### Returns

- `amount`: The amount of the deposit, in stablecoins. Scaled by \(10^{stablecoinDecimals}\).
- `maturationTimestamp`: The Unix timestamp at and after which the deposit will be able to be withdrawn. In seconds.
- `active`: `true` if the deposit hasn't been withdrawn, `false` otherwise.

##### `function sponsorDeposits(address user, uint256 depositID) external view returns (uint256 amount, uint256 maturationTimestamp, bool active)`

Returns info about a sponsor deposit.

###### Inputs

- `user`: The address of the sponsor.
- `depositID`: The index of the deposit in the `sponsorDeposits` array.

###### Returns

- `amount`: The amount of the deposit, in stablecoins. Scaled by \(10^{stablecoinDecimals}\).
- `maturationTimestamp`: The Unix timestamp at and after which the deposit will be able to be withdrawn. In seconds.
- `active`: `true` if the deposit hasn't been withdrawn, `false` otherwise.

##### `function UIRMultiplier() external view returns (uint256)`

Returns the multiplier \(m\) used [here](howitworks.md#technical-details). Scaled by \(10^{18}\).

##### `function MinDepositPeriod() external view returns (uint256)`

Returns the minimum deposit period, in seconds.

##### `function totalDeposit() external view returns (uint256)`

Returns the total deposited amount of `stablecoin`. Scaled by \(10^{stablecoinDecimals}\).

##### `function moneyMarket() external view returns (address)`

Returns the address of `MoneyMarket`.

##### `function stablecoin() external view returns (address)`

Returns the address of the stablecoin used.

##### `function feeModel() external view returns (address)`

Returns the address of `FeeModel`.

##### `function calculateUpfrontInterestRate(uint256 depositPeriodInSeconds) external view returns (uint256 upfrontInterestRate)`

Returns the current upfront interest rate \(\gamma\) used [here](howitworks.md#technical-details).

###### Inputs

- `depositPeriodInSeconds`: The length of the deposit's deposit period, in seconds.

##### `function blocktime() external view returns (uint256)`

Returns the average block time 88mph uses in its upfront interest rate calculation, in seconds. Scaled by \(10^{18}\).

##### `function deficit() external view returns (bool isNegative, uint256 deficitAmount)`

Returns the deficit of the pool.

###### Returns

- `isNegative`: Whether the deficit is negative. A negative deficit means there's a surplus.
- `deficitAmount`: Amount of the pool's deficit, in stablecoins. Scaled by \(10^{stablecoinDecimals}\).

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

##### `function totalValue() external view returns (uint256)`

Returns the total value locked in the money market, in terms of the underlying stablecoin. Scaled by \(10^{stablecoinDecimals}\).