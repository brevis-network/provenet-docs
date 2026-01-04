# Reward Distribution

The Brevis Prover Network utilizes a decoupled architecture where the `StakingController` serves as the central accounting engine for rewards and commissions while remaining agnostic to the specific reward source. This flexibility allows diverse engines—such as the marketplace and foundation incentive programs—to seamlessly integrate with the staking system.

***

### The Reward Mechanism

When a reward enters the `StakingController` from an external source, it is automatically processed through a standardized logic that balances prover compensation with staker yield.

#### 1. Prover Commission

Every prover can define **Commission Rates** (in basis points) that determine the percentage of a reward they retain to cover operational and hardware costs.

The system provides granular flexibility through a tiered commission structure:

* **Default Commission Rate**: Provers set a global fallback rate that applies to any reward source that does not have a specific override.
* **Per-Source Overrides**: Provers can customize their margin for different Reward Sources. For example, a prover may set a higher commission for `BrevisMarket` tasks to cover intensive GPU costs, while maintaining a lower rate for foundation rewards to maximize the yield passed to their stakers and remain competitive.

#### 2. Automated Staking Yield

The portion of the reward remaining after the prover's commission is "donated" directly into the prover's isolated **ERC-4626 Vault**.

* **Share Price Appreciation**: These assets increase the total value of the vault without minting new shares, causing the price per share to rise for all participants.
* **Seamless Compounding**: This mechanism ensures that stakers receive an automated, compounding yield on their assets without the need for manual claims or restaking actions.

***

### Flexible Reward Sources

Because the `StakingController` is decoupled from the reward logic, the network can support a wide variety of incentive streams.

#### Proving Fees (`BrevisMarket`)

Requesters pay Proving Fees in BREV token to have their specific computational tasks fulfilled. When a request is completed, the `BrevisMarket` contract processes the payment and forwards it to the `StakingController` to be distributed according to the winning prover's specific commission settings.

#### Foundation Rewards

To bootstrap network liquidity and support long-term growth, the protocol distributes foundation-funded rewards through a **Continuous Protocol Incentivization** model.

* **ZK-Verified Activities**: These rewards are distributed based on mathematical proofs of a prover's participation. This includes metrics such as active participation in auctions and the successful fulfillment of proof requests.
* **Stake-Based Allocation**: Rewards also take into account a prover's total stake (vault assets), ensuring that the distribution fairly considers the economic commitment provided to the network by the prover and their stakers.
* **Transparent Distribution**: By verifying activity off-chain and settling rewards on-chain, the foundation ensures a transparent and fair distribution that rewards the most reliable participants.
