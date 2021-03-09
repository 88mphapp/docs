# MPH tokenomics

## Initial issuance

An initial supply of 88,000 MPH was minted and will be distributed via liquidity mining.

It begins at 12:00 pm PT 11/20/2020 and lasts 14 days.

To participate, follow these steps:

1. Deposit into an 88mph fixed-rate APY pool. This will give you some upfront MPH.
2. Provide liquidity to [the MPH-ETH Uniswap pair](https://info.uniswap.org/pair/0x4d96369002fc5b9687ee924d458a7e5baa5df34e)
3. Stake the Uniswap LP token [here](https://88mph.app/farming)
4. Unstake when you want without penalty.

MPH token address: 0x8888801af4d980682e47f1a9036e589479e835c5

# Total supply
The MPH total supply depends on TVL's growth. Currently, 88mph incentivizes the lenders to deposit their funds in the [fixed-rate APY pools](https://88mph.app/deposits) by rewarding them with new MPH distributed according to an [issuance rate](https://88mph.app/docs/mph/#issuance-rate-multiplier). The upcoming governance will be in charge to monitor the protocol's parameters and decide from where the MPH rewards come from (new issuance and/or [governance treasury](https://88mph.app/docs/mph/#governance-treasury)). So it's up to the MPH holders to decide how to stimulate TVL growth, and by doing so, generate more revenues for [MPH stakers](https://88mph.app/docs/mph/#mph-staking-rewards-revenues-sharing), without diluting too much early adopters. 

Therefore, you can conclude that there isn't a maximum supply but the total supply is in the hand of the MPH holders.

You should also note that the MPH [circulating supply](https://academy.binance.com/en/glossary/circulating-supply) is a more accurate metric to access the current value of the protocol. The reason behind this statement is that the majority of the MPH distributed to lenders must be paid back when the lenders withdraw their principal. They are then sent to the [governance treasury](https://88mph.app/docs/mph/#governance-treasury). In other words, a big percentage of the MPH supply will end up in the governance treasury at any point in the future, excluding this supply from the open market and the [MPH staking](https://88mph.app/docs/mph/#mph-staking-rewards-revenues-sharing) system. More about the benefits of such design could be found on [Placeholder.vc](https://www.placeholder.vc/blog/2020/9/17/stop-burning-tokens-buyback-and-make-instead).

## Depositor rewards

When a user makes a deposit in an 88mph pool, they will receive newly-minted MPH tokens proportional to \(depositAmount \times depositPeriod\), which will be continuously vested over 270 days (the next release will make it equal to the deposit maturation). They can then stake this MPH in the MPH rewards pool to earn their share of protocol fees and yield-farming rewards. When the deposit is mature and the user wants to withdraw it, they will have to pay back a proportion of the MPH reward (currently between 30% and 90%). These MPH tokens will be sent to the 88mph governance treasury, where MPH holders can vote on how to spend them.

This distribution model ensures that existing users will have an amplified influence in the governance process and income sharing, which aligns the interest of the protocol with the interest of the users, rather than the interest of speculators.

## Bond buyer rewards

*Currently not activated*

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

88mph's protocol fee and yield-farmed tokens like $COMP need to accumulate in our MPH staking pool before allowing us to swap them for $DAI and distribute the rewards to $MPH stakers.

**Notes**: The MPH staking rewards APY is currently unknown because no fees were generated so far and the COMP harvested require time to accumulate. The 10% fee on the interest earned by lenders is applied only if someone withdraws his principal at maturity. An early withdrawal enquires no fee because the interest generated stays in the pool. It's not distributed to the original lender. It will generate compounded interests for everyone else staying in the fixed-rate APY pool.

You can claim and unstake when you want.

## Developer fund

Whenever MPH is minted by new deposits, an additional 10% of the minted amount is minted and sent to the developer fund. These MPH will be used to pay for future development & maintenance of the protocol.

Development funds: 0xfecBad5D60725EB6fd10f8936e02fa203fd27E4b

## Governance treasury
MPH holders have the power to shape the future of the protocol. A dedicated Snapshot will be live shortly after the protocol launch to enable community control.

As previously stated, the governance treasury receives the MPH tokens paid back by depositors when they withdraw their deposits. These MPH will be used by whatever the MPH holders decide on.


The governance process works by having users vote with their MPH tokens on various proposals ranging from protocol parameters to smart ways of using the capital assets stored in the treasury for creating new incentives, capitalization, and at the end growth.

Governance treasury: 0x56f34826Cc63151f74FA8f701E4f73C5EAae52AD
