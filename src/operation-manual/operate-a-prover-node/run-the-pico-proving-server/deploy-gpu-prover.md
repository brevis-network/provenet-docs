# Deploy GPU Prover

1. Follow [multi-machine-setup.md](https://github.com/brevis-network/pico-ethproofs/blob/main/docs/multi-machine-setup.md) to prepare the GPU box.
2.  Install [Docker](https://docs.docker.com/engine/install) and add your user to the `docker` group:

    ```bash
    sudo groupadd docker 2>/dev/null || true && sudo usermod -aG docker $USER
    ```

    If Docker reports `could not select device driver "" with capabilities: [[gpu]]`, install the [NVIDIA Container Toolkit](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/install-guide.html) and restart Docker:

    ```bash
    sudo systemctl restart docker
    ```
3.  Download the GPU Pico proving service image from `/home/ubuntu`:

    ```bash
    curl -sL -O https://pico-proofs.s3.us-west-2.amazonaws.com/prover-network/mainnet/pico-proving-service-gpu.tar
    ```
4.  Load the image into Docker:

    ```bash
    docker load -i pico-proving-service-gpu.tar
    ```
5.  Clone the repository and enter the GPU docker folder:

    ```bash
    git clone https://github.com/brevis-network/pico-proving-service
    cd pico-proving-service/docker/gpu
    ```
6.  Copy the environment template:

    ```bash
    cp .env.example .env
    ```

    * Fix `PROVER_COUNT` to the number of GPUs on your machine.
    * The `SPLIT_THRESHOLD`, `CHUNK_SIZE`, `MEM_POOL_RESERVE_SIZE` and `PICO_GPU_MEM` are set to default for RTX 5090. For RTX 4090, comment the settings for 5090 and enable the settings for 4090.&#x20;

    Leave the others unless you are sure they need to change. If you encounter a GPU memory allocation issue, you could enable `MAX_EMULATION_CYCLES` to give a try, its value is machine specific.
7.  Download dependencies and bring up the containers:

    ```bash
    make download-gnark
    make up
    ```
8.  Verify the containers:

    ```bash
    docker ps
    ```

    You should see `pico-proving-service` and `pico_gnark_server`. The Gnark server produces the final on-chain verifiable proof.
9.  Review the `Makefile` for other targets (down/restart/clean). For logs, run:

    ```bash
    make logs-server
    make logs-gnark
    ```
