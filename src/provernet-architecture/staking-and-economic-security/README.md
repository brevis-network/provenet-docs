# Staking & Economic Security

The Staking & Economic Security layer is the foundation for the network's operational reliability and incentive alignment. While zero-knowledge proofs provide the mathematical guarantee of correctness, this layer creates the economic framework that ensures the prover marketplace remains active, responsive, and highly available.

#### The Role of Capital in the Protocol

Staking in Brevis ProverNet is designed to align the interests of all participants. By requiring provers to maintain a stake, the protocol ensures that every actor is a committed stakeholder with a long-term interest in the network's health and reputation.

* **Operational Reliability**: Staking ensures that provers meet a high standard of performance, fostering a predictable and dependable marketplace for requesters.
* **Dynamic Eligibility**: A prover's capacity to bid on and win work is linked to their available stake, ensuring the network remains balanced and that every assignment is backed by sufficient collateral.

#### Advanced Vault Architecture

To ensure maximum safety and efficiency, the network utilizes an **Isolated Vault Architecture** based on the **ERC-4626** standard.

* **Risk Containment**: Every prover operates through a dedicated, isolated vault, ensuring that performance events are contained individually and protecting the broader network's liquidity.
* **DeFi Composability**: By following the ERC-4626 standard, vault shares are transferable ERC-20 tokens that can be easily integrated with the broader DeFi ecosystem.
* **Seamless Compounding**: Rewards are added directly into the vaults, increasing the asset-to-share ratio and allowing for automated reward compounding without the need for manual claims by stakers.

#### Economic Opportunity

The security layer is designed to reward participation through diversified yield streams:

* **Marketplace Fees**: Successfully fulfilling proof requests generates fees that flow back into the staking system, increasing the value of vault shares.
* **Foundation Rewards**: In addition to marketplace fees, the protocol distributes Foundation-funded rewards to provers based on their ZK-verified activities and total stake (vault assets).
* **Shared Success**: Both provers and stakers benefit from these combined revenue streams, creating a collaborative environment for scaling decentralized ZK-computation.
