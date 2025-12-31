# Send Proof Requests

To request a proof, in additions to the ELF binary and App ID, the input parameters of the App also need specified.

#### Generate Input Data and Public Values Digest

Use the [example apps](https://github.com/brevis-network/evm-pico-apps?tab=readme-ov-file#example-apps) as a template. They typically emit both the encoded input data and the public values digest expected by the prover. Example (Fibonacci, `n=100`):

```
VK_VERIFICATION=true cargo run -r --bin gen-inputs-fibonacci -- --n 100
```

Sample output:

```
=== For Onchain ProofRequest ===
vk: 0x00399db87f8d0d43e1795c4aebffe8cc58486e41b98371bdf667f3d29ce4476b
publicValuesDigest: 0x12c6f9f81993158e5d1e480b643b0466160893ebb0531e8c3ad7dd22c3fdeaa3
inputData: 0x01000000000000000400000000000000640000000000000000000000
```

Persist these values—they flow directly into the proof-request transaction.

### Send a Proof Request

The easiest way to submit requests is via the CLI utility, which batches multiple entries and handles encoding.&#x20;

1.  Clone the bidder ops repo from `/home/ubuntu`:

    ```bash
    git clone https://github.com/brevis-network/prover-network-bidder-ops
    ```
2.  From the `tools` directory, build the binary:

    ```
    cd tools
    go build
    ```
3.  Update the `[chain]` section in `config.toml`:

    | Field      | Description                                 |
    | ---------- | ------------------------------------------- |
    | keystore   | Path to your Ethereum account keystore JSON |
    | passphrase | Passphrase for the keystore                 |

    Adjust the parameters inside each `[[request]]` section as needed. Add multiple `[[request]]` sections to submit more than one proof request.
4.  Run:

    ```
    ./tools request-proof --config ./config.toml
    ```

Use the checklist below to fill each field in `[[request]]`  section correctly:

1. **`nonce`** – Unique identifier you choose per request.
2. **`vk`** – The verification key/app ID from Generate the Verification Key.
3. **`public_value_digest`** – Output from Generate Input Data and Public Values Digest.
4. **`img_url`** – URL hosting the ELF artifact built in Build the Application (ELF Artifact).
5. **`input_data`** – Encoded input from the generator step.
6. **`input_url`** – Optional URL pointing to the input payload when it is too large to inline. Provide either `inputData` or `inputURL` (or both).
7. **`fee` tuple (`max_fee`, `min_stake`, `deadline`)**:
   * `max_fee`: Maximum amount (USDC on Base) you are willing to pay for the proof.
   * `min_stake`: Minimum prover stake required to bid on this request.
   * `deadline`: Unix timestamp by which the proof must be submitted. The deadline must be within 30 days of the request time.
8. **`version`** - Version of the Pico verfifier to use, default to 0.

### Sample Apps for Testing

Use these reference programs when validating your setup:

* [**Fibonacci**](https://github.com/brevis-network/evm-pico-apps/tree/main/fibonacci) – Computes the _n_-th Fibonacci number from a `u32` input. Lightweight workload for quick smoke tests.
* [**Tendermint**](https://github.com/brevis-network/pico/tree/main/examples/tendermint/app) – Verifies consensus transitions between Tendermint light blocks. Medium-weight workload that exercises Merkle proofs and signature checks.
* [**Reth**](https://github.com/brevis-network/pico/tree/main/perf/bench_apps/reth-pico) – Executes Ethereum block verification via the Reth executor. Heavy workload representative of production proving jobs.

Build ELFs for these samples with `cargo pico build` inside each app directory, and use the matching input generators (where provided) to obtain the request payloads.
