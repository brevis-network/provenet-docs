# Proof Generation & Verification

This section details the internal architecture and operational workflow of a single prover's off-chain node, exploring how the **Bidder** and **Proving Service** components interact with the **Pico zkVM** to generate and verify trustless proofs on-chain.

***

### Pico: The High-Performance zkVM

The Brevis network utilizes Pico, an open-source, high-performance zero-knowledge virtual machine (zkVM). Pico serves as the execution engine for the network, providing several key technical advantages:

* **Modularity**: Composed of independent, interchangeable components that allow for tailored proof systems.
* **Flexibility**: Supports various proving backends and custom pipelines to fine-tune proof generation.
* **Extensibility**: Allows seamless integration of app-specific circuits and acceleration modules (coprocessors).
* **Performance**: Engineered for industry-leading proof generation speeds on standard hardware through optimized workflows and specialized circuits.

For detailed technical specifications and implementation guides, refer to the [Official Pico Documentation](https://pico-docs.brevis.network/).

***

### Off-Chain Prover Architecture

A Brevis prover node consists of two specialized service layers that communicate via a private RPC interface.

#### 1. The Proving Service

The Proving Service is the foundational execution engine. It acts as a generic Pico Prover instance that manages the raw computational resources of the node.

* **RPC Interface**: It exposes a private API to the Bidder for task management. This includes a `Cost Estimation` interface which returns the expected computation cost in machine cycles or returns an error if the request is invalid (unprovable).
* **Execution**: It receives proving tasks, executes the RISC-V bytecode, and generates the final ZK proof and public values digest.

#### 2. The Bidder

The Bidder serves as the operational manager, handling the economic and marketplace logic. Its workflow follows a strict sequence:

1. **Monitor**: It constantly scans the `BrevisMarket` contract for new proof requests that align with the prover’s stake and eligibility.
2. **Dry Run**: Before bidding, the Bidder performs a "dry run" by calling the Proving Service's Cost Estimation interface. This confirms the request is technically valid and provides the cycle count needed to decide if/how to participate in the bid.
3. **Task Management & Submission**: Once an auction is won, the Bidder sends the official proving task to the Proving Service, queries the status until the proof is generated, and subsequently facilitates the on-chain submission.

***

### Verification Workflow

The transition from a generated proof to a fulfilled on-chain request happens through a specific sequence of contract calls.

#### 1. On-Chain Submission

The Bidder calls the `submitProof` function on the BrevisMarket contract:

```solidity
// BrevisMarket.sol
function submitProof(bytes32 reqid, uint256[8] calldata proof)
```

#### 2. Pico Verification

The `BrevisMarket` acts as the orchestrator. It internally routes the proof to the PicoVerifier contract to ensure mathematical integrity:

```solidity
// PicoVerifier.sol
function verifyPicoProof(bytes32 riscvVkey, bytes32 publicValues, uint256[8] calldata proof)
```

If the PicoVerifier confirms the proof is valid, the `BrevisMarket` updates the status to Fulfilled, triggering the instant distribution of fees.
