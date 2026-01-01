# Use the ProverNet

The ProverNet enables decentralized ZK proof generation for custom computations via the Pico zkVM. This infrastructure separates the creation of computational logic from the actual request for proofs, allowing for a flexible ecosystem where different participants can interact with the network.

#### 1. [Write Applications](write-applications.md)

The application developer defines the computational logic in Rust, which compiles to RISC-V for execution within the Pico zkVM. This process produces a unique **verification key (vk)** and a **program image (elf)** that serve as the identifiers for the specific work to be performed on the network.

#### 2. [Send Proof Requests](send-proof-requests.md)

A request sender—which may be the application developer or an end-user—submits the program's vk and public inputs to the BrevisMarket contract. By attaching a fee, the sender triggers the marketplace auction process to have the computation proved and verified on-chain.

