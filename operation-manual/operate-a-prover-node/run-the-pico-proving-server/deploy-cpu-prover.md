# Deploy CPU Prover

1. Prepare the host:
   * Instance: AWS `r7i.16xlarge` (64 vCPUs) or equivalent.
   * OS: `ubuntu-24.04-amd64-server`.
   * Install prerequisites:
     * [Rust](https://www.rust-lang.org/tools/install) (restart the shell after installation).
     * Build tools: `sudo apt-get install -y build-essential cmake git pkg-config libssl-dev`
     * [sqlx-cli](https://github.com/launchbadge/sqlx/tree/main/sqlx-cli): `cargo install sqlx-cli`
2.  Install Docker and add your user to the `docker` group:

    ```bash
    sudo groupadd docker 2>/dev/null || true && sudo usermod -aG docker $USER
    ```
3.  Download the CPU image from `/home/ubuntu`:

    ```bash
    curl -sL -O https://pico-proofs.s3.us-west-2.amazonaws.com/prover-network/mainnet/pico-proving-service-cpu.tar
    ```
4.  Load the image:

    ```bash
    docker load -i pico-proving-service-cpu.tar
    ```
5.  Clone the repository and enter the CPU docker folder:

    ```bash
    git clone https://github.com/brevis-network/pico-proving-service
    cd pico-proving-service/docker/cpu
    ```
6.  Copy the environment template:

    ```bash
    cp .env.example .env
    ```

    Keep the default values unless you have a specific reason to override them.
7.  Download dependencies and start the containers:

    ```bash
    make download-gnark
    make up
    ```
8.  Check container status:

    ```bash
    docker ps
    ```

    You should see `pico-proving-service` and `pico_gnark_server`. The Gnark server produces the final on-chain verifiable proof.
9.  Review the `Makefile` for lifecycle targets (stop/restart/clean). For logs, run:

    ```bash
    make logs-server
    make logs-gnark
    ```
