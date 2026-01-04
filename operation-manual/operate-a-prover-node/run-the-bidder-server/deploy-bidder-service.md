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

    | Field                  | Description                                                                     |
    | ---------------------- | ------------------------------------------------------------------------------- |
    | `prover_url`           | Pico proving service gRPC endpoint (`${pico-ip}:${port}`, default port `50052`) |
    | `prover_eth_addr`      | Ethereum address of the prover account                                          |
    | `submitter_keystore`   | Path to the submitter keystore JSON (or AWS KMS reference)                      |
    | `submitter_passphrase` | Keystore password (or `apikey:apisec` for AWS KMS)                              |

    Optional tuning fields:

    | Field                | Description                                                                                                                                         |
    | -------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------- |
    | `prover_gas_price`   | Price per prove cycle (bid fee = `prove_cycles * prover_gas_price / 1e12`). Cycles are auto-computed; set the price based on your operational costs |
    | `prove_min_duration` | Skip requests whose remaining time (reveal → deadline) is less than this many seconds                                                               |
    | `max_input_size`     | `0` means no limit; otherwise skip requests with larger inputs                                                                                      |
    | `max_fee`            | Skip requests whose bid fee would exceed this ceiling                                                                                               |
    | `vk_whitelist`       | If empty, accept all requests. Otherwise process only verification keys (VKs) on this list                                                          |
    | `vk_blacklist`       | Skip requests targeting VKs on this list                                                                                                            |

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
