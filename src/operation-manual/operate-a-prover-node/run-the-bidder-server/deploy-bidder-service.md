# Deploy Bidder Service

### Prepare EC2 machine and install dependencies

1. Launch an Ubuntu EC2 instance with appropriate security groups and a key pair.
2.  Install Go (>=1.16):

    ```bash
    sudo snap install go --classic
    mkdir -p "$HOME/go/bin"
    ```
3.  Install CockroachDB:

    ```bash
    curl -sL https://binaries.cockroachdb.com/cockroach-v24.2.3.linux-amd64.tgz \
      | sudo tar -xz --strip 1 -C /usr/local/bin cockroach-v24.2.3.linux-amd64/cockroach
    sudo chmod +x /usr/local/bin/cockroach
    ```
4.  Configure CockroachDB as a systemd service:

    ```bash
    sudo mkdir -p /var/log/crdb
    sudo touch /var/log/crdb/out.log /var/log/crdb/err.log

    sudo tee /etc/systemd/system/crdb.service << EOF
    [Unit]
    Description=CockroachDB single node
    After=network-online.target

    [Service]
    WorkingDirectory=/home/ubuntu
    ExecStart=/usr/local/bin/cockroach start-single-node --insecure --listen-addr=localhost:26257 \
        --http-addr=localhost:18080 --store=path=/home/ubuntu/db
    StandardOutput=append:/var/log/crdb/out.log
    StandardError=append:/var/log/crdb/err.log
    Restart=always
    User=ubuntu
    Group=ubuntu
    RestartSec=10

    [Install]
    WantedBy=multi-user.target
    EOF

    sudo systemctl enable crdb.service
    sudo systemctl start crdb.service
    ```
5.  Configure Go environment variables by appending the following to `$HOME/.profile`:

    ```bash
    export GOBIN=$HOME/go/bin
    export GOPATH=$HOME/go
    export PATH=$PATH:$GOBIN
    ```

    Then reload:

    ```bash
    source $HOME/.profile
    ```

### Setup binary, db, config and accounts

1.  Clone the bidder service repo from `/home/ubuntu`:

    ```bash
    git clone https://github.com/brevis-network/prover-network-bidder
    cd prover-network-bidder
    git checkout main
    ```
2.  Initialize the CockroachDB schema:

    ```bash
    cat $HOME/prover-network-bidder/dal/schema.sql | cockroach sql --insecure
    ```
3.  Build and install the bidder binary:

    ```bash
    cd ./cmd/service
    go build -o bidder
    cp ./bidder $HOME/go/bin
    cd ~
    ```
4.  Copy bidder configs:

    ```bash
    git clone https://github.com/brevis-network/prover-network-ops
    cp -a prover-network-ops/node-configs ~/.bidder
    ```
5.  Edit `~/.bidder/config.toml`:

    You may use separate accounts for staking (`prover`) and for bidding/submitting proofs (`submitter`), or reuse the same Ethereum account for both.

    <table><thead><tr><th width="206.34375">Field</th><th>Description</th></tr></thead><tbody><tr><td><code>prover_url</code></td><td>Pico proving service gRPC endpoint (<code>${pico-ip}:${port}</code>, default port <code>50052</code>)</td></tr><tr><td><code>prover_eth_addr</code></td><td>Ethereum address of the prover account</td></tr><tr><td><code>submitter_keystore</code></td><td>Path to the submitter keystore JSON (or AWS KMS reference)</td></tr><tr><td><code>submitter_passphrase</code></td><td>Keystore password (or <code>apikey:apisec</code> for AWS KMS)</td></tr></tbody></table>

    Optional tuning fields:

    <table><thead><tr><th width="205.27734375">Field</th><th>Description</th></tr></thead><tbody><tr><td><code>prover_gas_price</code></td><td>Price per prove cycle (bid fee = <code>prove_cycles * prover_gas_price / 1e12</code>). Cycles are auto-computed; set the price based on your operational costs</td></tr><tr><td><code>prove_min_duration</code></td><td>Skip requests whose remaining time (reveal → deadline) is less than this many seconds</td></tr><tr><td><code>max_input_size</code></td><td><code>0</code> means no limit; otherwise skip requests with larger inputs</td></tr><tr><td><code>max_fee</code></td><td>Skip requests whose bid fee would exceed this ceiling</td></tr><tr><td><code>vk_whitelist</code></td><td>If empty, accept all requests. Otherwise process only verification keys (VKs) on this list</td></tr><tr><td><code>vk_blacklist</code></td><td>Skip requests targeting VKs on this list</td></tr></tbody></table>

    > Note: (1) Fees are denominated in the staking token. (2) A VK digest is generated when building the ELF binary and uniquely identifies a ZK program.

### Run the bidder service

1.  Create a systemd service:

    ```bash
    sudo mkdir -p /var/log/bidder
    sudo touch /var/log/bidder/app.log

    sudo tee /etc/systemd/system/bidder.service << EOF
    [Unit]
    Description=bidder daemon
    After=network-online.target

    [Service]
    Environment=HOME=/home/ubuntu
    ExecStart=/home/ubuntu/go/bin/bidder --config /home/ubuntu/.bidder/config.toml
    StandardOutput=append:/var/log/bidder/app.log
    StandardError=append:/var/log/bidder/app.log
    Restart=always
    RestartSec=3
    User=ubuntu
    Group=ubuntu
    LimitNOFILE=4096

    [Install]
    WantedBy=multi-user.target
    EOF
    ```
2.  Create `/etc/logrotate.d/bidder` and add the following:

    ```
    /var/log/bidder/*.log {
        compress
        copytruncate
        daily
        maxsize 30M
        rotate 30
    }
    ```
3.  Enable and start the service:

    ```bash
    sudo systemctl enable bidder
    sudo systemctl start bidder
    ```
