# Request Fulfillment

Request Fulfillment is the final stage of the proof lifecycle. This phase transitions the proof from off-chain computation to on-chain verification, concluding with the automated settlement of fees and the handling of any service failures.

***

### Proof Generation & Submission

Once a request is assigned, the winning prover must deliver a valid cryptographic proof before the specified Deadline.

* **Off-Chain Execution**: The prover runs the computation and generates a ZK proof using the Brevis ZK-VM. Success in this phase depends on both high-performance hardware and service reliability to ensure the proof is ready within the required timeframe.
* **On-Chain Submission**: The prover submits the completed proof to the `BrevisMarket` contract, which calls the PicoVerifier to programmatically verify the proof's validity. If the proof is correct, the request status is updated to Fulfilled.

***

### Fee Distribution

When a request is successfully fulfilled, the protocol handles the distribution of the escrowed fees atomically. This ensures immediate compensation for the work performed.

The fee is distributed according to the following hierarchy:

1. **Protocol Fee**: The `BrevisMarket` contract takes a small cut as a protocol fee, which is sent to the network treasury.
2. **Prover Commission**: From the remaining amount, the prover’s commission (based on their default or source-specific rate) is deducted and sent directly to their designated wallet.
3. **Staking Yield**: The final portion is deposited into the operator's ProverVault. This increases the value of the vault's underlying assets, causing an immediate share price appreciation that serves as compounding yield for all stakers.

***

### Failure Handling

The marketplace provides automated logic to protect requesters and penalize unreliable behavior when a proof is not delivered.

#### Refund

If a request is not fulfilled, the requester can reclaim their escrowed `maxFee` by triggering a refund. This is available if the auction ended without any prover participation, or if the assigned prover failed to submit a verified proof before the deadline.

#### Slashing

Reliability is enforced through economic accountability. If an assigned prover fails to fulfill their commitment by the deadline, they may be penalized based on the `minStake` of the request and the configured `slashBps`. All slashed funds are transferred from the prover's vault directly to the protocol treasury, ensuring that provers only bid on tasks they are capable of completing.
