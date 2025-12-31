# Submitter Management

Submitter Management provides the operational security framework for provers. It allows operators to separate their high-value staking assets from the "hot" keys used for automated, high-frequency network interactions.

***

### Operational Security

By using authorized **Submitter Addresses**, provers can keep their primary **Staking Key** in cold storage. This "hot-cold" architecture provides two main benefits:

* **Risk Mitigation**: If a worker node is compromised, the attacker only gains access to an operational key. They cannot withdraw or steal the underlying stake held in the ProverVault.
* **Infrastructure Scaling**: A single prover entity can authorize multiple worker nodes across different regions to submit proofs, enhancing redundancy without exposing their primary identity.

***

### Submitter Registration

To ensure a secure link between an operator and their worker nodes, the `BrevisMarket` requires a **two-step handshake** to register a submitter:

1. **Submitter Consent**: The Submitter Address (the worker node) must first call `setSubmitterConsent` on the marketplace contract. This signals that the address is willing to be managed by a specific prover.
2. **Prover Registration**: The Staking Key then calls `registerSubmitter` to finalize the link.

This mutual agreement ensures that no address can be mistakenly or maliciously linked to a prover's identity without consent from both parties. Once registered, the worker node is authorized to bid on auctions and submit proofs on behalf of the prover.

The Staking Key maintains the power to call `unregisterSubmitter` at any time to immediately revoke a worker node's authority, allowing for rapid key rotation or infrastructure updates.
