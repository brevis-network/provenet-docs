# Initialize Prover Account

Brevis provides CLI utilities that streamline on-chain operations to initalize a prover.

1.  Clone the bidder ops repo from `/home/ubuntu`:

    ```bash
    git clone https://github.com/brevis-network/prover-network-bidder-ops
    ```
2.  From the `tools` directory in this repo, build the binary:

    ```bash
    cd tools
    go build
    ```
3.  Update `config.toml` with the following fields:

    <table><thead><tr><th width="140.55078125">Section</th><th width="212.1171875">Field</th><th>Description</th></tr></thead><tbody><tr><td>chain</td><td>keystore</td><td>Path to your prover Ethereum account keystore JSON</td></tr><tr><td>chain</td><td>passphrase</td><td>Passphrase for the prover keystore</td></tr><tr><td>init_prover</td><td>submitter_keystore</td><td>(Optional) Submitter keystore if using a different account</td></tr><tr><td>init_prover</td><td>submitter_passphrase</td><td>(Optional) Passphrase for the submitter account</td></tr><tr><td>init_prover</td><td>prover_name</td><td>Name that identifies you or your organization</td></tr><tr><td>init_prover</td><td>prover_icon</td><td>URL of the icon that represents you or your organization</td></tr></tbody></table>
4.  Run:

    ```bash
    ./tools init-prover --config ./config.toml
    ```

> Note that this step will also auto stake a configured minimum amount BREV token to ensure prover meets the minimum self-stake requirement.
