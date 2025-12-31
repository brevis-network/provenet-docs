# Write Applications

Brevis offers Pico client SDK to facilitate the writing of the applications that can be proved by prover node.&#x20;

#### Install Pico CLI

Use Pico CLI `v1.2.2` for the steps below.

**Option 1: Install via Cargo**

```
cargo +nightly-2025-08-04 install --git https://github.com/brevis-network/pico --tag v1.2.2 pico-cli
```

Verify the installation:

```
cargo pico --version
```

**Option 2: Install from a Local Checkout**

```
git clone https://github.com/brevis-network/pico
cd pico
git checkout v1.2.2
cd sdk/cli
cargo install --locked --force --path .
```

#### Write and Build the Application (ELF Artifact)

Follow the [Pico documentation](https://pico-docs.brevis.network/writing-apps/programs) and the [EVM Pico apps examples](https://github.com/brevis-network/evm-pico-apps) to write your program. Build it into an ELF with:

```
cd <app-name>
cargo pico build
cd ..
```

It's the binary in ELF format that will be registered and loaded into prover nodes.&#x20;

#### Generate the Verification Key (App ID)

The Verification Key (App ID) uniquely identifies an app. Every ELF has a deterministic verification key. Use the helper in `evm-pico-apps`:

```
VK_VERIFICATION=true cargo run -r --bin gen-app-id -- --elf <app-name>/elf/riscv32im-pico-zkvm-elf
```

Example (Fibonacci app):

```
VK_VERIFICATION=true cargo run -r --bin gen-app-id -- --elf fibonacci/elf/riscv32im-pico-zkvm-elf
```

Sample output:

```
Generated app_id: 0x00399db87f8d0d43e1795c4aebffe8cc58486e41b98371bdf667f3d29ce4476b
```

When asking the ProverNet for a proof, the ELF binary and the App ID are required.
