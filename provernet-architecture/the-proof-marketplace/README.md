# The Proof Marketplace

The **BrevisMarket** is the decentralized coordination engine of the network. It acts as a trustless, on-chain marketplace that matches computational demand from applications with the high-performance supply of independent provers.

By facilitating open competition, the marketplace ensures that requesters receive the most competitive pricing and lowest latency, while provers are rewarded for their hardware efficiency and software optimizations.

***

### Core Marketplace Principles

The marketplace is designed to ensure the network remains open, secure, and reliable through three fundamental properties:

* **Permissionless Matching**: Any application can submit a proof request, and any registered prover can bid on available work. There are no centralized gatekeepers; matching is determined purely by the protocol's mathematical auction rules.
* **Trustless Execution**: The marketplace does not "trust" a prover to be honest. Instead, it relies on the `PicoVerifier` to programmatically verify every proof result before any fees are released from escrow.
* **Economic Accountability**: The marketplace is deeply integrated with the `StakingController`. Provers must maintain a sufficient stake to participate, ensuring they have "skin in the game" to fulfill their assignments on time and with high integrity.

***

### Proof Request Lifecycle

While the smart contract tracks the technical status of a request, the actual lifecycle involves both on-chain transitions and off-chain coordination.

#### Conceptual Stages vs. On-Chain Status

<table data-header-hidden><thead><tr><th width="165.84765625"></th><th width="160.55078125"></th><th></th></tr></thead><tbody><tr><td><strong>Conceptual Stage</strong></td><td><strong>On-Chain Status</strong> </td><td><strong>What is Happening?</strong></td></tr><tr><td>Submission</td><td><code>Pending</code></td><td>The requester escrows the fee and the request is broadcast to the network.</td></tr><tr><td>Auction</td><td><code>Pending</code></td><td>Provers submit and reveal sealed bids. The request remains <code>Pending</code> on-chain during this phase.</td></tr><tr><td>Assigned</td><td><code>Pending</code></td><td>The auction window closes. A winner is identified and begins generating the ZK proof.</td></tr><tr><td>Fulfilled</td><td><code>Fulfilled</code></td><td>The prover submits the proof. The contract confirms it and distributes fees instantly.</td></tr><tr><td>Refunded</td><td><code>Refunded</code></td><td>If a request is not fulfilled by the deadline, the requester can trigger a refund of their fees.</td></tr></tbody></table>

***

### Key Marketplace Components

* [Request Auction](request-auction.md): The competitive bidding process where provers compete for jobs. It is designed to encourage provers to bid their actual costs while ensuring requesters pay a fair market rate.
* [Request Fulfillment](request-fulfillment.md): The process of generating ZK proofs, submitting them for on-chain verification, and the subsequent automatic distribution of fees or refunds.
* [Overcommit Protection](overcommit-protection.md): A reliability safeguard that limits how much work a prover can take on at once based on their available stake, preventing the network from becoming over-leveraged.
* [Submitter Management](submitter-management.md): The operational framework that allows provers to authorize specific node addresses to submit work, separating high-security staking keys from day-to-day operational keys.
