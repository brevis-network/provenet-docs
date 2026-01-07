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
    curl -sL -O https://pico-proofs.s3.us-west-2.amazonaws.com/prover-network/mainnet/pico_proving_service_gpu_v1_0_1.tar
    ```
4.  Load the image into Docker:

    <pre class="language-bash"><code class="lang-bash"><strong># If old image exists, delete it firstly
    </strong>docker rmi -f pico-proving-service-gpu:latest
    <strong>
    </strong><strong># Load the new downloaded image
    </strong><strong>docker load -i pico_proving_service_gpu_v1_0_1.tar
    </strong>docker tag pico-proving-service-gpu:v1.0.1 pico-proving-service-gpu:latest
    </code></pre>
5.  Clone the repository and enter the GPU Docker folder:

    ```bash
    # must switch to tag `v1.0.1` if you have already cloned pico-proving-service
    git clone --branch v1.0.1 https://github.com/brevis-network/pico-proving-service
    cd pico-proving-service/docker/gpu
    ```
6.  Copy the environment template:

    ```bash
    cp .env.example .env
    ```

    * Fix `PROVER_COUNT` to the number of GPUs on your machine.
    * The `SPLIT_THRESHOLD`, `CHUNK_SIZE`, `MEM_POOL_RESERVE_SIZE` and `PICO_GPU_MEM` are set to default for RTX 5090. For RTX 4090, comment the settings for 5090 and enable the settings for 4090.&#x20;

    Leave the others unless you are sure they need to change. If you encounter a GPU memory allocation issue, try enabling `MAX_EMULATION_CYCLES`. Its value is machine specific.
7.  Download dependencies and bring up the containers:

    <pre class="language-bash"><code class="lang-bash"># delete the old gnark files for upgrade if exist
    rm -rf ../gnark_downloads
       
    <strong>make download-gnark
    </strong>make up
    </code></pre>
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
