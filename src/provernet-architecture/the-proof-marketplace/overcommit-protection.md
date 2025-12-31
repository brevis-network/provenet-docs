# Overcommit Protection

The Overcommit Protection mechanism is a core reliability safeguard that balances **capital efficiency** with **network security**. It defines the maximum concurrent workload a prover can handle relative to their total stake, ensuring the network remains stable and responsive.

***

### The Concept of Overcommitment

In a strict 1:1 collateral model, a prover would be limited to taking on tasks where the total `minStake` requirements exactly match their locked assets. However, requiring 100% collateral for every concurrent task can be capital-inefficient for reliable, professional operators.

Overcommitment allows the network to scale its throughput by permitting provers to manage a larger volume of "Active" tasks than their raw stake would normally allow. This ensures that the network's computational capacity is not artificially capped by the speed of token liquidity.

***

### Capacity Management

The marketplace tracks the Assigned Stake of every prover. A prover’s ability to bid on a new request is determined by the following eligibility rule:

```
Required Stake = Request.minStake + (AssignedStake * overcommitBps / 10,000)
```

* **Assigned Stake**: The sum of the `minStake` requirements for all requests currently assigned to a prover that have not yet reached a final state (Fulfilled or Refunded).
* **The Overcommit Parameter** (`overcommitBps`): A protocol-wide variable that determines how much "weight" is given to existing assignments when calculating a prover's required collateral for a new job.
* **Capacity Release**: As soon as a request is completed, the associated `minStake` is removed from the prover’s Assigned Stake tally, immediately restoring their capacity to bid.

***

### Throughput vs. Safety

The `overcommitBps` value is a vital lever for tuning the network's economic health. Its value represents a direct trade-off between the total volume of proofs the network can handle and the level of collateral backing those proofs.

* **Higher `overcommitBps` (Safety-Focused)**: By increasing this value, the protocol makes existing assignments "consume" more of the prover's stake. This leads to higher collateralization per task and reduces systemic risk if a single prover fails, but it requires more total capital to maintain high network throughput.
* **Lower `overcommitBps` (Throughput-Focused)**: A lower value reduces the "weight" of existing assignments, allowing a prover to take on a significantly higher volume of proofs with the same amount of total stake. This maximizes capital efficiency for provers but means a larger portion of the network's active work is shared against the same pool of collateral.
