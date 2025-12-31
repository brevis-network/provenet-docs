# Request Auction

The Request Auction represents the first half of the proof lifecycle. It is the phase where computational demand meets supply, transforming a user's `ProofRequest` into a formal assignment for a specific prover. This process is governed by the `BrevisMarket` contract to ensure that every job is matched with a capable and economically committed operator.

***

### The Proof Request

A `ProofRequest` is a set of cryptographic and economic constraints that define how the proof must be generated. When a requester submits a job, they specify the following parameters:

* **Max Fee**: The maximum amount (in BREV tokens) the requester is willing to pay. This amount is escrowed on-chain upon submission.
* **Deadline**: The strict time limit by which the proof must be submitted to the on-chain verifier.
* **Minimum Stake**: The minimum collateral a prover must hold in their vault to be eligible to bid. This ensures the prover has sufficient "skin in the game" relative to the task's value.
* **Proof Specification**: The technical requirements for the ZK proof, including the target circuit verification key and the data to be verified.

***

### Prover Eligibility

Not every prover can bid on every request. The marketplace automatically filters participants based on their real-time status:

1. **Active Status**: Only provers in the Active state (those meeting the global Minimum Self-Stake) can participate in auctions.
2. **Collateral Sufficiency**: A prover must have enough available stake in their ProverVault to meet the request’s `minStake`. The [Overcommit Protection](overcommit-protection.md) mechanism governs the extent to which a prover can take on additional concurrent workload relative to their total stake.
3. **Reliability & Performance**: To meet the deadline, provers must maintain high hardware performance and service reliability. Significant downtime or hardware lag can prevent a prover from fulfilling an assignment, putting their stake at risk.

***

### The Bidding Mechanism

The auction uses a **Commit-Reveal** version of a **Sealed-Bid Reverse Second-Price** model. This ensures a fair and competitive environment while preventing front-running.

#### Phase 1: Commitment (The Bid Hash)

During the bidding window, provers submit a Bid Hash to the blockchain.

* **Confidentiality**: While the hash is public, the actual bid amount remains hidden. This prevents competitors from seeing and strategically undercutting bids by a few wei.
* **Integrity**: Once a hash is submitted, the prover cannot change their bid amount later.

#### Phase 2: Reveal (Price Discovery)

After the bidding window closes, the Reveal Phase begins. Bidders submit the preimage (the actual bid amount and secret) that matches their previously submitted hash.

* **Tracking the Winner**: The on-chain contract tracks the lowest revealed bid in real-time. Once the reveal phase ends, the winner becomes immutable on-chain.
* **The Second-Price Rule**: To encourage truthful bidding, the winner is paid the **second-lowest bid** (capped by the requester's `maxFee`). This ensures provers bid their true operational costs while receiving a fair market premium.

***

### Assignment

Once the reveal phase is complete and the winner is finalized on-chain, the request is considered **Assigned**.

The winner's required stake is now logically committed to this request. The prover then enters the execution phase to generate the ZK proof, marking the start of [Request Fulfillment](request-fulfillment.md).
