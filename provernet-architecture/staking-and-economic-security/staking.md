# Staking

The staking system provides the economic framework for prover participation. By requiring provers to lock collateral, the protocol ensures a highly active and responsive marketplace where every actor has a measurable commitment to the network's operational success.

***

### Prover Lifecycle

The `StakingController` manages the journey of an operator through a defined set of states and requirements to ensure all active participants remain economically aligned with the protocol.

#### Prover States

* **Null**: The initial state for any operator before registration or vault initialization.
* **Active**: The prover is fully operational and eligible to bid on and fulfill proof requests in the marketplace.
* **Deactivated**: A cooldown or exit state. The prover can no longer take on new work but remains in the system to fulfill or mature existing unstake requests.
* **Jailed**: An administrative penalty state. Jailed provers are ineligible for new work until they are reactivated by an authorized administrator.

#### Registration & Initialization

Operators enter the network by calling `initializeProver`. This function triggers two simultaneous actions:

* **Vault Deployment**: The deterministic deployment of a dedicated ProverVault (ERC-4626) specifically for that operator.
* **Initial Deposit**: An initial stake that satisfies the minimum self-stake requirement, moving the operator directly from the Null state to the Active state.

#### Self-Stake Requirement

To maintain an Active status, provers must adhere to strict collateral rules:

* **Mandatory Collateral**: Provers must maintain a specific amount of their own assets (defined by `minSelfStake`) within their vault at all times.
* **Non-Transferable Shares**: The portion of vault shares representing the minimum self-stake is locked and cannot be transferred while the prover is active.
* **Automatic Enforcement**: If a prover’s self-stake falls below the required threshold, the system transitions them to the Deactivated state to ensure all active work is properly collateralized.

***

### The Staking Process

Asset management within the network is handled through isolated vaults, with a streamlined process for both provers and their stakers.

* **Direct Staking**: Users can stake with any active prover by depositing BREV tokens into the `StakingController`. The controller manages the deposit into the isolated vault and issues vault shares to the user.
* **Compounding Yield**: Rewards from the marketplace or foundation are added directly to the vault assets. This increases the value of all shares automatically, allowing for seamless compounding without manual claims.

***

### Two-Phase Unstaking Process

To ensure provers cannot bypass commitments to pending work, all withdrawals follow a mandatory two-phase process:

1. **Phase 1: Request (`requestUnstake`)**: A user initiates an unstake by specifying the number of vault shares to redeem. The shares are burned, and the equivalent asset value enters a mandatory Unstake Delay period.
2. **Phase 2: Completion (`completeUnstake`)**: After the delay period has passed, the user calls `completeUnstake` to transfer the BREV tokens from the vault directly to their wallet.

> Note: Assets in the "Pending" phase remain part of the prover's economic accountability until the second phase is completed, ensuring provers remain responsive to assignments even during their exit period.
