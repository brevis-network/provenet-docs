# Send Proof Requests

Once you have your application’s vk and ELF file, you can request a proof by preparing specific input data and interacting with the marketplace. This process triggers the auction mechanism where a prover will be assigned to fulfill your request.

***

#### 1. Prepare Inputs and Public Values

Before sending a request, you must generate the data for the specific execution instance you want to prove. This step produces the `inputData` (raw inputs) and the `publicValuesDigest` (a commitment to the program's outputs).

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

> Important: Save these values accurately. They are the exact parameters required for the `requestProof` transaction; any mismatch will cause the on-chain verification to fail.

***

#### 2. Send a Proof Request

While production applications will typically implement their own programmatic request logic, Brevis provides a **CLI utility** as a reference implementation and testing tool. Developers can use the CLI source code to understand how to correctly encode parameters and interact with the `BrevisMarket` contract.

**Using the CLI Reference Tool**

1.  **Installation**: Clone the repository and build the tool binary:

    ```bash
    git clone https://github.com/brevis-network/prover-network-ops
    cd prover-network-bidder-ops/tools
    go build -o tools
    ```
2. **Configuration**: Update the `[chain]` section in `config.toml` with your `keystore` path and `passphrase`. Then, add one or more `[[request]]` sections using the checklist below:
   * `nonce`: A unique identifier you choose per request.
   * `vk`: The verification key/app ID generated in the previous step.
   * `public_value_digest`: The output from the input generator.
   * `img_url`: URL hosting the compiled ELF artifact.
   * `input_data`: Encoded input from the generator step.
   * `input_url`: (Optional) URL for the input payload if it is too large to inline.
   * `version`: Version of the Pico verifier (defaults to 0).
   * `fee`:
     * `max_fee`: Maximum fee you are willing to pay for the proof.
     * `min_stake`: Minimum prover stake required to bid on this request.
     * `deadline`: Unix timestamp for proof submission (must be within 30 days).
3.  **Execution**: Run the command to submit your requests:

    ```bash
    ./tools request-proof --config ./config.toml
    ```

***

#### Sample Apps for Testing

Use these reference programs to validate your setup and observe how different workloads perform on the network:

* [Fibonacci](https://github.com/brevis-network/evm-pico-apps/tree/main/fibonacci): Computes the _n_-th Fibonacci number from a `u32` input. This is a lightweight workload ideal for quick smoke tests and connectivity checks.
* [Tendermint](https://github.com/brevis-network/pico/tree/main/examples/tendermint/app): Verifies consensus transitions between Tendermint light blocks. This is a medium-weight workload that exercises Merkle proofs and signature checks.
* [Reth](https://github.com/brevis-network/pico/tree/main/perf/bench_apps/reth-pico): Executes Ethereum block verification via the Reth executor. This is a heavy workload representative of production-grade proving jobs.
