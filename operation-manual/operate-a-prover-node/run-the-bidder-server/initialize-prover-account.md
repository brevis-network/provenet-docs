# Initialize Prover Account

Before your bidder server can participate in the marketplace, you must register your prover identity on-chain. ProverNet is currently deployed on Base Mainnet.&#x20;

Because the BREV token is originally issued on Ethereum, you must [**bridge**](https://cbridge.celer.network/1/8453/BREV) your tokens to Base to meet the self-staking requirements (currently 1000 BREV). The BREV token address on Base is `0x086F405146Ce90135750Bbec9A063a8B20A8bfFb`.

### Prover and Submitter Roles

To maximize security, ProverNet decouples the prover and submitter roles. This allows you to maintain your prover account (which holds your staked BREV and identity) in a secure hardware or cold wallet, while running the automated bidder service with a separate submitter hot wallet that signs and submits proofs operationally.

### Register Your Prover Identity

We recommend using a hardware wallet for your prover account to ensure the security of your staked assets.

The current Staking Controller contract requires a minimum self-stake of 1000 BREV. This amount is automatically transferred and staked during the `initializeProver` transaction.

**Option A: Direct Contract Interaction (Recommended for Hardware Wallets)**

Use a block explorer (e.g., BaseScan) to interact directly with the contracts using your hardware wallet. Perform these steps with your prover account:

1. Approve Staking: Visit the [BREV contract](https://basescan.org/token/0x086F405146Ce90135750Bbec9A063a8B20A8bfFb#writeContract) and call `approve(0x9c0D..., 1000000000000000000000)` (for 1000 BREV) for the [StakingController](https://basescan.org/address/0x9c0D8C5F10f0d3A02D04556a4499964a75DBf4A3#writeProxyContract).
2. Initialize Prover: On the [StakingController](https://basescan.org/address/0x9c0D8C5F10f0d3A02D04556a4499964a75DBf4A3#writeProxyContract), call `initializeProver` with your desired commission rate in basis points (500 bps = 5%). This call will automatically transfer the 1000 BREV minimum stake from your wallet.
   * Default Rate: Applied to all reward sources without a specific override.
   * Per-Source Override: Use `setCommissionRate(source, rate)` for specific rates (e.g., set a higher 50% or 5000 bps rate for `BrevisMarket` to cover GPU costs).
3. Set Profile: Call `setProverProfile` to publish your metadata (name, icon).
4. Authorize the Submitter: If your submitter is a separate account, you must link them on the [BrevisMarket contract](https://basescan.org/address/0xcCec2a9FE35b6B5F23bBF303A4e14e5895DeA127#writeProxyContract):
   * As the Submitter: Call `setSubmitterConsent`.
   * As the Prover: Call `registerSubmitter` to authorize that address.

**Option B: Use the CLI Utility (Alternative for Hot Wallets)**

If you are using a standard keystore-based hot wallet, the `init-prover` tool automates the registration, the 1000 BREV staking, and submitter authorization in a single workflow.

1.  **Build the Tool**:

    ```bash
    git clone https://github.com/brevis-network/prover-network-bidder-ops
    cd prover-network-bidder-ops/tools
    go build
    ```
2.  **Update `config.toml`**:

    <table data-header-hidden><thead><tr><th width="119.5546875"></th><th width="275.5546875"></th><th></th></tr></thead><tbody><tr><td><strong>Section</strong></td><td><strong>Field</strong></td><td><strong>Description</strong></td></tr><tr><td>chain</td><td><code>keystore</code></td><td>Path to your prover account keystore JSON.</td></tr><tr><td>chain</td><td><code>passphrase</code></td><td>Passphrase for the prover keystore.</td></tr><tr><td>init_prover</td><td><code>submitter_keystore</code></td><td>(Optional) Submitter keystore if using a separate account.</td></tr><tr><td>init_prover</td><td><code>submitter_passphrase</code></td><td>(Optional) Passphrase for the submitter account.</td></tr><tr><td>init_prover</td><td><code>prover_name</code></td><td>Name that identifies you or your organization.</td></tr><tr><td>init_prover</td><td><code>prover_icon</code></td><td>URL of the icon that represents your organization.</td></tr><tr><td>init_prover</td><td><code>default_commission_rate_bps</code></td><td>Default commission (e.g., 500 = 5%).</td></tr><tr><td>init_prover</td><td><code>proof_fee_commission_rate_bps</code></td><td>(Optional) BrevisMarket specific rate (e.g., 5000 = 50%).</td></tr></tbody></table>
3.  **Run**:

    ```bash
    ./tools init-prover --config ./config.toml
    ```
