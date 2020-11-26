# MPH tokenomics

## Initial issuance

An initial supply of 88,000 MPH were minted and will be distributed via liquidity mining.

It begins at 12:00 pm PT 11/20/2020 and lasts 14 days.

To participate, follow these steps:

1. Deposit into an 88mph fixed-rate APY pool. This will give you some upfront MPH.
2. Provide liquidity to [the MPH-ETH Uniswap pair](https://info.uniswap.org/pair/0x4d96369002fc5b9687ee924d458a7e5baa5df34e)
3. Stake the Uniswap LP token [here](https://88mph.app/farming)
4. Unstake when you want without penalty.

MPH token address: 0x8888801af4d980682e47f1a9036e589479e835c5

## Depositor rewards

When a user makes a deposit in an 88mph pool, they will receive newly-minted MPH tokens proportional to \(depositAmount \times depositPeriod\), which will be continuously vested over 7 days. They can then stake this MPH in the MPH rewards pool to earn their share of protocol fees and yield-farming rewards. When the deposit is mature and the user wants to withdraw it, they will have to pay back a proportion of the MPH reward (currently 90%). These MPH tokens will be sent to the 88mph governance treasury, where MPH holders can vote on how to spend them.

This distribution model ensures that existing users will have an amplified influence in the governance process and income sharing, which aligns the interest of the protocol with the interest of the users, rather than the interest of speculators.

## Bond buyer rewards

When a user buys a floating-rate bond, they will receive newly-minted MPH tokens when one of the deposits whose debt the bond funded is withdrawn, proportional to \(depositAmount \times timeFromBondPurchaseToDepositMaturation\). They can then stake this MPH in the MPH rewards pool to earn their share of protocol fees and yield-farming rewards.

This reward can also have vesting like depositor rewards, though for now, we don't think it's necessary to enable that. 

**Notes**: at the beginning, bonds buyers won't get MPH rewards. Only Fixed-rate deposits users. And we won't allow deposit and bond MPH rewards at the same time.

## Issuance rate multiplier

The multiplier’s unit is MPH per second per stablecoin (of deposit).

So a good way to think about what a smart issuance rate multiplier is: given x stablecoins deposit for 1 year, how much MPH should be rewarded?

We preset the issuance rate at relaunch with a lower issuance rate than v1 launch. Temporary MPH reward are distributed to your address over a 7-day vesting period and you keep your permanent MPH reward at maturity. If early withdraw, then 100% of the reward need to be paid back and they are burnt (not adding to the total supply of MPH).

We’ll revisit the depositors’ reward shortly after relaunch according to the evolution of the MPH price and the TVL growth in each pool.

Note: for issuance rate updates, we use the governance multisig directly as the contract owner. It allows us more flexibility in these early days. We will transition to a timelock after the dust has settled. The protocol's parameters will be ended over to the community token holders when the governance system will be launched.

The core team vision is low issuance for the foreseeable future to don’t wreck the liquidity mining program, and don't dilute too much early adopters, while allowing the growth of the deposits TVL to generate sustainable profits for MPH stakers (so we'll probably incentivize more the Compound pools to harvest more COMP). That's the goal and it'll be a test & learn process other the coming days/weeks.


## MPH Staking rewards (Revenues sharing)

By staking your MPH, you can claim your share of rewards from the 88mph rewards pool where the protocol fee and yield-farming rewards are collected.

88mph distribute 100% of its revenues with the community:

* 88mph protocol fee: 10% is deducted from the interest when a depositor withdraws.
* Yield-farming rewards: yield-farmed tokens earned from the protocols 88mph is connected to (COMP, etc.).

88mph's protocol fee and yield-farmed tokens like $COMP need to accumulate in our MPH staking pool before allowing us to swap them for $DAI and distribute the rewards to $MPH stakers. We'll call the function later when there is a sufficient amount of rewards to distribute.

The rewards are distributed in DAI. You can claim and unstake when you want.

## Developer fund

Whenever MPH is minted by new deposits, an additional 10% of the minted amount is minted and sent to the developer fund. These MPH will be used to pay for future development & maintenance of the protocol.

Development funds: 0xfecBad5D60725EB6fd10f8936e02fa203fd27E4b

## Governance treasury
MPH holders have the power to shape the future of the protocol. A dedicated Snapshot will be live shortly after the protocol launch to enable community control.

As previously stated, the governance treasury receives the MPH tokens paid back by depositors when they withdraw their deposits. These MPH will be used by whatever the MPH holders decide on.


The governance process works by having users vote with their MPH tokens on various proposals ranging from protocol parameters to smart ways of using the capital assets stored in the treasury for creating new incentives, capitalization, and at the end growth.

Governance treasury: 0x56f34826Cc63151f74FA8f701E4f73C5EAae52AD
