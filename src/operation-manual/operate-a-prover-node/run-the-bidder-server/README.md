# Run the Bidder Server

The Bidder Server is the operational orchestrator of your prover node. It manages the business logic and marketplace interactions required to fulfill proof requests and secure rewards.

#### Operational Role

The Bidder automates the proof request lifecycle by bridging your off-chain Proving Service with the on-chain BrevisMarket. It is responsible for monitoring requests, performing "dry runs" to verify provability, managing auction bids, and submitting finalized proofs for settlement.

#### Setup Workflow

The deployment process is divided into two phases:

1. [**Initialize Prover Account**](initialize-prover-account.md): Establishing your on-chain identity via the StakingController, depositing stake, and authorizing Submitter Addresses to secure your assets.
2. [**Deploy Bidder Service**](deploy-bidder-service.md): A comprehensive guide to preparing your machine, installing necessary dependencies, and launching the Bidder binary to connect with your Proving Service.

