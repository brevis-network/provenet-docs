# Operate a Prover Node

Operating a Brevis prover node is a high-responsibility role that transforms computational power into network security. As an operator, your primary goal is to maintain a reliable environment that fulfills ZK proof requests within deadlines to earn protocol fees and staking yields.

#### The Operational Stack

While the architecture is detailed in the [previous section](../../provernet-architecture/proof-generation-and-verification.md#off-chain-prover-architecture), your daily operations revolve around two primary software components:

* **Pico Proving Server**: The core execution engine that handles RISC-V ZK proof generation.
* **Bidder Server**: The orchestrator that manages marketplace participation, bidding strategy, and on-chain submissions.

#### Setup & Management Lifecycle

Becoming an active prover involves a streamlined path from infrastructure setup to active participation:

1. **Hardware & Environment**: Provisioning high-performance hardware and stable network connectivity to ensure proofs are generated and submitted within the required timeframe.
2. **Software Deployment**: First deploy the Proving Server to establish your computational baseline, followed by the Bidder Server to connect that power to the `BrevisMarket`.
3. **Prover Account Initialization**: Setting up your prover profile via the `StakingController`. We also recommend authorizing Submitter Addresses (worker keys) to handle automated on-chain actions while keeping your primary staking key safe (in cold storage).
