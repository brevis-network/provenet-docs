# Write Applications

To utilize the ProverNet, you must first develop your application logic and compile it for the Pico zkVM to generate its unique identifier.

***

#### 1. Install Pico CLI

The Pico CLI is required to build programs and manage zkVM artifacts. Please use version `v1.2.2`.

**Option 1: Install via Cargo**

```bash
cargo +nightly-2025-08-04 install --git https://github.com/brevis-network/pico --tag v1.2.2 pico-cli
```

**Option 2: Install from a Local Checkout**

```bash
git clone https://github.com/brevis-network/pico
cd pico
git checkout v1.2.2
cd sdk/cli
cargo install --locked --force --path .
```

**Verify the installation:**

```bash
cargo pico --version
```

***

#### 2. Build the Application (ELF Artifact)

Pico applications are compiled into a RISC-V Executable and Linkable Format (ELF) file.

* Refer to the [Official Pico Documentation](https://pico-docs.brevis.network/writing-apps/programs) for application structure.
* Explore [EVM Pico App Examples](https://github.com/brevis-network/evm-pico-apps) for pre-built templates.

To build your program into an ELF, run:

```
cd <app-name>
cargo pico build
cd ..
```

***

#### 3. Generate the Verification Key (App ID)

Every program ELF has a unique, deterministic **Verification Key (VK)**, which serves as the **App ID** on ProverNet. This key is static for a specific version of your code.&#x20;

Use the helper tool in the `evm-pico-apps` repository to extract it:

```bash
VK_VERIFICATION=true cargo run -r --bin gen-app-id -- --elf <app-name>/elf/riscv32im-pico-zkvm-elf
```

Example (Fibonacci app):

```bash
VK_VERIFICATION=true cargo run -r --bin gen-app-id -- --elf fibonacci/elf/riscv32im-pico-zkvm-elf
```

Sample output:

```
Generated app_id: 0x00399db87f8d0d43e1795c4aebffe8cc58486e41b98371bdf667f3d29ce4476b
```

This **app\_id** (the `vk`) along with the **ELF** is what you will use in your proof requests to tell the network exactly which program logic needs to be executed.
