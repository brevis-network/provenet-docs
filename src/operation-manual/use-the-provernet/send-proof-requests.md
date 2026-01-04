# Send Proof Requests

To request a proof from the marketplace, you must submit a transaction to the `BrevisMarket` contract. This page details the data structure required by the contract and the tools available to submit it.

***

#### 1. The ProofRequest Structure

Every proof request is defined by the `ProofRequest` struct. Whether you use the CLI or a custom script, these are the parameters you must provide:

```solidity
struct ProofRequest {
    uint64 nonce;               // Unique ID for the request
    bytes32 vk;                 // The Verification Key (App ID)
    bytes32 publicValuesDigest; // Commitment to the program outputs
    string imgURL;              // URL to the compiled ELF artifact
    bytes inputData;            // Encoded input data (raw bytes)
    string inputURL;            // (Optional) URL to input data if payload is large
    uint32 version;             // Verifier version (default: 0)
    FeeParams fee;              // Economic parameters for the auction
}

struct FeeParams {
    uint96 maxFee;    // Max BREV to pay for the proof
    uint96 minStake;  // Minimum prover stake required to bid
    uint64 deadline;  // Unix timestamp; proof must be submitted by this time
}
```

***

#### 2. Prepare Inputs and Public Values

Before filling the `ProofRequest` struct, you must generate the execution-specific data. This produces the `inputData` and the `publicValuesDigest`.

Use the [example apps](https://github.com/brevis-network/evm-pico-apps?tab=readme-ov-file#example-apps) as a template for generating these values. For instance, using the Fibonacci app with `n=100`:

```bash
VK_VERIFICATION=true cargo run -r --bin gen-inputs-fibonacci -- --n 100
```

Sample output:

```
=== For Onchain ProofRequest ===
vk: 0x00399db87f8d0d43e1795c4aebffe8cc58486e41b98371bdf667f3d29ce4476b
publicValuesDigest: 0x12c6f9f81993158e5d1e480b643b0466160893ebb0531e8c3ad7dd22c3fdeaa3
inputData: 0x01000000000000000400000000000000640000000000000000000000
```

> Note: Ensure these values are captured accurately. Any mismatch between the `inputData` and `publicValuesDigest` will cause the on-chain verification to fail.

***

#### 3. Send a Proof Request

While production applications will typically implement their own programmatic request logic using the struct above, Brevis provides a CLI utility as a reference implementation and testing tool.

**Using the CLI Reference Tool**

1.  **Installation**: Clone the repository and build the tool binary:

    ```bash
    git clone https://github.com/brevis-network/prover-network-ops
    cd prover-network-bidder-ops/tools
    go build -o tools
    ```
2. **Configuration**: Update the `[chain]` section in `config.toml` with your `keystore` path and `passphrase`. Add your request details in the `[[request]]` section using the parameters derived in the previous steps.
3.  **Execution**: Run the command to submit your requests:

    ```bash
    ./tools request-proof --config ./config.toml
    ```

***

#### Sample Apps for Testing

Use these reference programs to validate your setup:

* [Fibonacci](https://github.com/brevis-network/evm-pico-apps/tree/main/fibonacci): Computes the _n_-th Fibonacci number. A lightweight workload for quick smoke tests.
* [Tendermint](https://github.com/brevis-network/pico/tree/main/examples/tendermint/app): Verifies consensus transitions. A medium-weight workload that exercises Merkle proofs and signature checks.
* [Reth](https://github.com/brevis-network/pico/tree/main/perf/bench_apps/reth-pico): Executes Ethereum block verification. A heavy workload representative of production proving jobs.
