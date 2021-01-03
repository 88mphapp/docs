# MPH 代币经济

MPH是88mph的代币，首次铸币量为88000 MPH，通过流动性挖矿的方式分发给用户。

初次发行于太平洋时间2020年11月20日中午12：00（即北京时间2020年11月21日凌晨4：00），共持续14天。

MPH代币合约地址：0x8888801af4d980682e47f1a9036e589479e835c5

# 总供应量

MPH代币总供应量取决于总锁仓价值（Total Value Locked）的增长。当前，我们激励用户向我们的 [流动池子](https://88mph.app/deposits)存款的机制主要是，根据[发行率](https://88mph.app/docs/mph/#issuance-rate-multiplier)向用户发放更多MPH币作为奖励。正在孵化的社区共治机制将负责监控各项平台参数，并对作为奖励的MPH代币来源有决定权：重新铸币发放或者从[社区治理经费](https://88mph.app/docs/mph/#governance-treasury)中获取。因此，MPH持有者有权决定如何刺激总锁仓价值增长，通过这种方式，[MPH锁仓挖矿用户](https://88mph.app/docs/mph/#mph-staking-rewards-revenues-sharing)能够获得更多收益，也不会过分削弱早期用户的决策权。

至此，可以说MPH币的总供应量并没有极值，但MPH持仓用户对供应量有决策权。

另外，MPH 的[循环供应](https://academy.binance.com/en/glossary/circulating-supply)也可作为衡量此平台投资价值的一个重要指标。原因在于，在用户收回本金时，存款第一周分发给用户的MPH代币中有一部分须被返还。这些MPH将存入[社区治理经费](https://88mph.app/docs/mph/#governance-treasury)中。换言之，这部分MPH不会流入开放市场和[MPH锁仓挖矿](https://88mph.app/docs/mph/#mph-staking-rewards-revenues-sharing)体系，而将成为社区治理经费的一部分，详见[Placeholder.vc](https://www.placeholder.vc/blog/2020/9/17/stop-burning-tokens-buyback-and-make-instead)。

## 存款人奖励

用户在88mph流动池中存入一笔资金后，将获得一定数额的新铸MPH币作为回报，数额与\(存款数额\*存款时间\)成正比，持续发放七日以上。接下来，用户可以将这些MPH币锁入MPH收益池中，赚取服务费或者耕作收益。当存期满想要取出资金时，用户须将一定比例（当前在30%-90%之间浮动）的MPH收益返还给社区。这部分MPH币将成为88mph社区治理经费，由MPH持仓用户投票决定如何使用它们。
This distribution model ensures that existing users will have an amplified influence in the governance process and income sharing, which aligns the interest of the protocol with the interest of the users, rather than the interest of speculators.
这种分发模型能有效确保老用户在社区治理和收益分配过程中的话语权，整个社区收益与用户收益息息相关，形成良性循环，降低投机者使坏的机会。
## Bond buyer rewards
## 债券买家奖励
When a user buys a floating-rate bond, they will receive newly-minted MPH tokens when one of the deposits whose debt the bond funded is withdrawn, proportional to \(depositAmount \times timeFromBondPurchaseToDepositMaturation\). They can then stake this MPH in the MPH rewards pool to earn their share of protocol fees and yield-farming rewards.
用户购买浮动利率债券，当一笔由该债券供资的存款被提取时，该用户会获得一定数额的新铸MPH币，数额与\（存款数额\*从购买债券到存款到期的时间\）成正比。用户可以将这些MPH币锁入MPH收益池中，赚取服务费或者耕作收益。
This reward can also have vesting like depositor rewards, though for now, we don't think it's necessary to enable that. 
债券买家奖励也可以采用存款人奖励的发放方式，不过眼下我们觉得没必要。
**Notes**: at the beginning, bonds buyers won't get MPH rewards. Only Fixed-rate deposits users. And we won't allow deposit and bond MPH rewards at the same time.
**注意**：债券买家不会在购买后马上获得MPH奖励，只有固定利率存款用户才会即时获得奖励；并且，我们不允许存款奖励和债券奖励同时发放给同一用户。
## Issuance rate multiplier
## 发行率乘数
The multiplier’s unit is MPH per second per stablecoin (of deposit).
假设用户本金是美元，发行率乘数的单位是，MPH每美元每秒。
So a good way to think about what a smart issuance rate multiplier is: given x stablecoins deposit for 1 year, how much MPH should be rewarded?
因此，可以这样思考这个发行率乘数的合理性：本金X，存期一年，存款人将获得多少MPH奖励？
We preset the issuance rate at relaunch with a lower issuance rate than v1 launch. Temporary MPH reward are distributed to your address over a 7-day vesting period and you keep your permanent MPH reward at maturity. If early withdraw, then 100% of the reward need to be paid back and they are burnt (not adding to the total supply of MPH).
部署者的预设是，第二次的发行率总是低于前一次的数字。MPH奖励会在7天内分发到您的地址，除去要归还的部分，永久的MPH奖励会一直保留在您的账户内。如果提前取出本金，那么您须归还全部的MPH奖励，这部分将会被销毁，永久减少MPH的总供应量。
We’ll revisit the depositors’ reward shortly after relaunch according to the evolution of the MPH price and the TVL growth in each pool.
发行率重置后，我们会根据MPH价格的变化和每个池中总锁仓价值的增长重新评估储户的奖励。
Note: for issuance rate updates, we use the governance multisig directly as the contract owner. It allows us more flexibility in these early days. We will transition to a timelock after the dust has settled. The protocol's parameters will be ended over to the community token holders when the governance system will be launched.
注意：对于发行率的更新，我们直接使用参与社区治理成员的多重签名作为其合约所有者，这会让我们在当下的孵化阶段灵便许多。等社区成熟时，我们将转而使用时间锁定（Timelock）合约。启动治理系统时，协议参数会转交给社区币持有者。
The core team vision is low issuance for the foreseeable future to don’t wreck the liquidity mining program, and don't dilute too much early adopters, while allowing the growth of the deposits TVL to generate sustainable profits for MPH stakers (so we'll probably incentivize more the Compound pools to harvest more COMP). That's the goal and it'll be a test & learn process other the coming days/weeks.
部署团队的核心愿景是在可预见的短期内，发币量保持在较低水平，以免破坏流动性采矿计划，也不会稀释过多的早期用户，同时允许总锁仓价值增量为MPH利益相关者产生可持续的利润（因此，我们可能会启动更多的复合池以收获更多的COMP币）。对于本团队而言，这是一个小目标，是一个学习过程，也是接下来数周内需要经历的考验。

## MPH Staking rewards (Revenues sharing)
## MPH 锁仓奖励（收益分红）
By staking your MPH, you can your share of rewards from the 88mph rewards pool where the protocol fee and yield-farming rewards are collected.
社区服务费和耕作奖励都收集在88mph奖励池中，一旦你stake你的MPH币，就可以向88mph奖励池索取你的奖励份额。
88mph distribute 100% of its revenues with the community:
本平台的所有收益都将与整个社区共享。
* 88mph protocol fee: 10% is deducted from the interest when a depositor withdraws.
* Yield-farming rewards: yield-farmed tokens earned from the protocols 88mph is connected to (COMP, etc.).
* 88mph 服务费：当储户取出本金时，将从其利息里扣除10%作为社区服务费。
* 挖矿奖励：从88mph平台获得的挖矿收益代币COMP等相关联。
88mph's protocol fee and yield-farmed tokens like $COMP need to accumulate in our MPH staking pool before allowing us to swap them for $DAI and distribute the rewards to $MPH stakers.
88mph的服务费和耕作奖励，比如COMP币，在您允许我们把COMP换成DAI并将奖励分发给参与MPH锁仓的用户前，将在我们MPH锁仓矿池中持续积累。
**Notes**: The MPH staking rewards APY is currently unknown because no fees were generated so far and the COMP harvested require time to accumulate. The 10% fee on the interest earned by lenders is applied only if someone withdraws his principal at maturity. An early withdrawal enquires no fee because the interest generated stays in the pool. It's not distributed to the original lender. It will generate compounded interests for everyone else staying in the fixed-rate APY pool.
**注意**：MPH锁仓奖励的年收益率暂不可知，因为到目前为止还没有产生任何费用，也需要时间来积累COMP收益。从利息中扣除10%服务费的收取对象仅限那些存期满时取出本金的用户。提前取出本金的储户无须扣费，因为他们在存期未满时间内获得的利息将保留在资金池中，不会给到储户。这部分利息将为其他储户带来额外收益。
You can claim and unstake when you want.
你想什么时候索取和解锁你的奖励都可以。
## Developer fund
## 开发者基金
Whenever MPH is minted by new deposits, an additional 10% of the minted amount is minted and sent to the developer fund. These MPH will be used to pay for future development & maintenance of the protocol.
一旦有新存款流入池中，有新的MPH币铸成，都将有一笔额外的MPH币同时铸成，数额是该笔新铸币的10%，并收入开发者基金。这些额外的MPH将用于社区未来的发展和维护。
Development funds: 0xfecBad5D60725EB6fd10f8936e02fa203fd27E4b
开发者基金合约地址：0xfecBad5D60725EB6fd10f8936e02fa203fd27E4b
## Governance treasury
## 社区治理经费
MPH holders have the power to shape the future of the protocol. A dedicated Snapshot will be live shortly after the protocol launch to enable community control.
MPH 持有用户有权决定平台的未来。在平台启动后不久，将启用专门的快照以保证社区治理机制良好运作。
As previously stated, the governance treasury receives the MPH tokens paid back by depositors when they withdraw their deposits. These MPH will be used by whatever the MPH holders decide on.
如前所述，社区治理经费的来源是储户取款时归还的MPH。这些MPH的用途由MPH持有者全权决定。

The governance process works by having users vote with their MPH tokens on various proposals ranging from protocol parameters to smart ways of using the capital assets stored in the treasury for creating new incentives, capitalization, and at the end growth.
社区治理的运作机制是，用户使用他们的MPH币对不同议案进行投票，议案内容多样，包括各项平台参数，如何使用社区治理经费（开创新的激励机制，资本化，最终增长）等等。
Governance treasury: 0x56f34826Cc63151f74FA8f701E4f73C5EAae52AD
社区治理经费合约地址：0x56f34826Cc63151f74FA8f701E4f73C5EAae52AD
