# Integration guide

## Smart contract

To interact with an 88mph pool, you will mostly call the pool's DInterest contract. You can find the source code [on our GitHub](https://github.com/88mphapp/88mph-contracts/blob/master/contracts/DInterest.sol), and the reference is available [here](https://88mph.app/docs/smartcontract/#dinterest).

### Creating a deposit

Creating a deposit has two steps:

1. Give ERC-20 approval to the DInterest contract, the amount of which is at least the deposit amount.
2. Call `DInterest::deposit()` or `DInterest::multiDeposit()`.

Once an account makes a deposit, the account will also receive vested MPH rewards. Check [Withdrawing vested MPH rewards](#withdrawing-vested-mph-rewards) to learn more.

#### Example

```solidity
DInterest pool = DInterest(0x35966201A7724b952455B73A36C8846D8745218e); // Compound DAI pool
ERC20 token = ERC20(0x6b175474e89094c44da98b954eedeac495271d0f); // DAI
uint256 depositAmount = 3 * 10 ** 18; // 3 DAI
uint256 maturationTimestamp = now + 365 days;

require(token.approve(address(pool), depositAmount));
pool.deposit(depositAmount, maturationTimestamp);

uint256 depositID = pool.depositsLength(); // the ID of the deposit
```

### Withdrawing a deposit

Withdrawing a deposit has the following steps:

1. Approve the MPH payback amount to the MPHMinter contract.
2. Call `DInterest::withdraw()` or `DInterest::multiWithdraw()`. If you're withdrawing a deposit before its maturation date, call `DInterest::earlyWithdraw()` or `DInterest::multiEarlyWithdraw()`.

To withdraw a deposit whose debt was funded by a floating-rate bond, you need to input the ID of the floating-rate bond as an argument (fundingID). To obtain this value, you can use our [GraphQL API](https://thegraph.com/explorer/subgraph/bacon-labs/eighty-eight-mph).

#### Example

Let's say we want to withdraw a deposit from the Compound DAI pool with ID 10. First, we make a GraphQL query to obtain the fundingID.

```graphql
{
  deposits(where: {pool: "0x35966201a7724b952455b73a36c8846d8745218e", nftID: 10}) {
    fundingID
  }
}
```

The response would be

```graphql
{
  "data": {
    "deposits": [
      {
        "fundingID": "4"
      }
    ]
  }
}
```

Now we know the fundingID is 4. Next, we make the smart contract call.

```solidity
MPHToken mph = MPHToken(0x8888801af4d980682e47f1a9036e589479e835c5);
DInterest pool = DInterest(0x35966201A7724b952455B73A36C8846D8745218e); // Compound DAI pool
MPHMinter mphMinter = pool.mphMinter();
uint256 depositID = 10;
uint256 fundingID = 4;

mph.approve(address(mphMinter), uint256(-1)); // Infinite approval
pool.withdraw(depositID, fundingID);
```

### Withdrawing vested MPH rewards

Each deposit (and in the future, floating-rate bond) yields vested MPH rewards to the user. This is handled by the [Vesting contract](https://github.com/88mphapp/88mph-contracts/blob/master/contracts/rewards/Vesting.sol). The vesting is linear and continuous, and is done over 7 days (though this value may change).

In order to withdraw vested MPH, you need to call `Vesting::withdrawVested(address account, uint256 vestIdx)`. You need the index of a vesting object, `vestIdx`, in order to withdraw the MPH. There are several important things to note:

1. A vesting object's index is not tied to the ID of a deposit or a floating-rate bond. Vesting MPH to another account is something that anyone can do by calling `Vesting::vest()`, which would increment the highest vest index. Even though it is likely the case that `vestIdx = depositID - 1` (`vestIdx` is 0-indexed while `depositID` is 1-indexed), there should be contingencies for handling cases where this is not true. For instance, you can have a function that allows you to call `withdrawVested()` with an arbitrary `vestIdx` and update whatever state you need updated.
2. `withdrawVested()` can be used to withdraw vested MPH to any account by any account, so it is possible (but not likely) that someone would call it for your contract and vest MPH to it. You would want a function that can deal with this situation, which should have the same logic as a function that handles MPH that's mistakenly sent to your contract.

#### Example

```solidity
Vesting vesting = Vesting(0x8943eb8F104bCf826910e7d2f4D59edfe018e0e7);
address account = ...;
uint256 vestIdx = ...;

vesting.withdrawVested(account, vestIdx);
```

### Buying a floating-rate bond

Buying a floating-rate bond takes two steps:

1. Approve deposit tokens to the DInterest contract, the amount of which should be at least the cost of the bond.
2. Call `DInterest::fundAll()` or `DInterest::fundMultiple()` to either fund all deposits with outstanding debt or a subset of them.

Computing the exact cost of the bond is slightly complicated if you call `fundMultiple()`, if you're interested you can checkout the source code [here](https://github.com/88mphapp/88mph-contracts/blob/787e9571951840897fe4b36f9bf3d4b493620c5b/contracts/DInterest.sol#L292). If you call `fundAll()` then the cost is just `DInterest::surplus()`. It is usually okay to just give infinite approval to save gas and simplify the code.

#### Example

```solidity
DInterest pool = DInterest(0x35966201A7724b952455B73A36C8846D8745218e); // Compound DAI pool
ERC20 token = ERC20(0x6b175474e89094c44da98b954eedeac495271d0f); // DAI

require(token.approve(address(pool), uint256(-1))); // Infinite approval
pool.fundAll();

uint256 fundingID = pool.fundingListLength(); // The ID of the floating-rate bond object
```

## REST API

We offer a REST API for fetching basic info of 88mph pools. The endpoint is at [https://api.88mph.app/pools](https://api.88mph.app/pools). The only supported method is GET.

### Example response

```json
[
  {
    "address": "0xb1abaac351e06d40441cf2cd97f6f0098e6473f2", // the pool's DInterest contract
    "token": "0x5b5cfe992adac0c9d48e05854b2d91c73a003858", // the deposit token address
    "tokenSymbol": "CRV:HUSD", // the deposit token symbol
    "protocol": "Harvest", // the pool's underlying lending protocol (e.g. Compound, Aave, Harvest)
    "oneYearInterestRate": "3.6621277863445296", // the fixed interest rate, in percent (e.g. 3.6 means 3.6%)
    "mphAPY": "60.8988450243270707253", // the MPH APY after deducting the payback amount, in percent
    "totalValueLockedInToken": "1548211.678555032702255836", // the TVL denominated in the deposit token
    "totalValueLockedInUSD": "1548211.678555032702255836" // the TVL denominated in USD
  },
  ...
]
```