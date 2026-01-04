# System Overview

The Brevis Prover Network (ProverNet) is a decentralized infrastructure that enables smart contracts to offload complex, data-heavy computations to an off-chain network without sacrificing security.

By utilizing Zero-Knowledge (ZK) proofs, the network allows a smart contract to verify that a piece of off-chain work was performed correctly. This architecture allows the blockchain to act as an enforcement and settlement layer for expensive computations such as analyzing historical states or complex data transitions, which would otherwise be prohibitively expensive to perform on-chain.

***

### The Network Architecture

The network functions through a continuous, trustless loop between **on-chain enforcement** and **off-chain execution**. The off-chain provers perform the heavy computation, while the on-chain contracts ensure that the rules of the protocol are followed and that only mathematically correct results are accepted.

#### 1. On-Chain Enforcement (The Rules & Verdict)

The smart contract suite serves as the final source of truth for the network's state and rules:

* **Economic Alignment & Accountability**: The `StakingController` manages prover identity and ensures every participant is a committed stakeholder with "skin in the game". This framework aligns prover incentives by enabling them to earn rewards and yields proportional to their stake and performance.
* **The Marketplace**: The `BrevisMarket` acts as a trustless escrow and auction house, defining how work is assigned and payments are distributed to ensure a fair and competitive environment for all participants.
* **Cryptographic Verification**: The `PicoVerifier` (Brevis zkVM) provides the final cryptographic verdict. It programmatically enforces that any result submitted by a prover matches the requested computation before any rewards are released, guaranteeing the integrity of the work performed.

#### 2. Off-Chain Execution (The Work)

The off-chain Prover Nodes are the active participants that power the network:

* **Market Monitoring**: Provers continuously monitor the `BrevisMarket` for new `ProofRequests` that match their hardware capabilities and risk profile.
* **Competitive Bidding**: Prover nodes run automated logic to participate in reverse auctions, bidding the most competitive fees based on local operational costs.
* **ZK Proof Generation**: Once a prover wins an auction, they execute the requested computation and generate a ZK proof using optimized zkVM software and high-performance hardware.
* **Proof Submission**: The completed proof is submitted to the blockchain to trigger the on-chain verification and settlement process.

***

### Core Network Roles

* **Requesters**: Smart contracts or users that need trustless, verifiable computation.
* **Provers**: Off-chain operators running Brevis node software and high-performance ZK-generation hardware.
* **Stakers**: BREV token holders who provide economic security (collateral) for provers in exchange for a share of the generated fees.

***

### Proof Lifecycle

The lifecycle of a single request follows a strict path to ensure reliability:

1. **Request**: A user or dApp submits a `ProofRequest` and escrows a maximum fee on-chain.
2. **Auction**: Off-chain provers compete in a sealed-bid reverse auction to win the task at the most competitive price.
3. **Work**: The assigned prover generates the ZK proof off-chain using the optimized Brevis zkVM.
4. **Verification**: The proof is submitted to the blockchain and verified by the `PicoVerifier`.
5. **Settlement**: Upon successful verification, the fee is automatically distributed to the prover and their stakers.\[\_test]
