# Run the Pico Proving Server

The Pico Proving Server is the computational backend of your node, responsible for executing RISC-V bytecode and generating ZK proofs. It operates as a standalone service that receives tasks and cost-estimation requests from the Bidder Server via a private RPC interface.

#### Choosing Your Hardware

Your choice of hardware directly determines your node’s capability and reliability within the marketplace. Because ZK proof generation is computationally intensive, your environment must be powerful enough to meet the strict deadlines set by requesters:

* **GPU Proving (Recommended)**: For production environments, a GPU host is strongly recommended. For many complex proof requests, a high-performance GPU is the only viable option to generate the proof within the required deadline.
* **CPU Proving**: A CPU host is suitable for small workloads, experimentation, or initial testing. While it allows you to verify your node's configuration, it may not be sufficient for the high-throughput or low-latency tasks found in the live marketplace.

#### Next Steps

The subsections below outline the specific installation steps, system dependencies, and configuration requirements for each setup:

* [**Deploy GPU Prover**](deploy-gpu-prover.md): Optimized setup for production-grade performance.
* [**Deploy CPU Prover**](deploy-cpu-prover.md): Standard setup for testing and lightweight workloads.
