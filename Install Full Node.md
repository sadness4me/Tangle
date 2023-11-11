# Update server system (Ubuntu 22.04)
```
sudo apt update && sudo apt upgrade -y
sudo apt install curl iptables build-essential git wget jq make gcc nano tmux htop nvme-cli pkg-config libssl-dev libleveldb-dev libgmp3-dev tar clang bsdmainutils ncdu unzip llvm libudev-dev make protobuf-compiler -y
```
# Install Rust
```
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
# choose 1
source $HOME/.cargo/env
# Configure Rust
rustup default nightly
rustup update
rustup update nightly
rustup target add wasm32-unknown-unknown --toolchain nightly
git clone https://github.com/webb-tools/tangle.git
cd tangle
cargo build --release
```
# Install Node
```
cd
mkdir -p $HOME/.tangle && cd $HOME/.tangle
wget -O tangle https://github.com/webb-tools/tangle/releases/download/v5.0.0/tangle-standalone-linux-amd64
chmod 744 tangle
sudo -s
mv tangle /usr/bin/
tangle --version
# 0.5.0-e892e17-x86_64-linux-gnu
```
# Download json
```
wget -O $HOME/.tangle/tangle-standalone.json "https://raw.githubusercontent.com/webb-tools/tangle/main/chainspecs/testnet/tangle-standalone.json"
chmod 744 ~/.tangle/tangle-standalone.json
```
# Create a service file
In this guide, I set up node with automatically generate keys. If you want to create your private keys by yourself, refer [this section](https://www.docs.webb.tools/docs/ecosystem-roles/validator/required-keys/)
```
sudo tee /etc/systemd/system/tangle.service > /dev/null << EOF
[Unit]
Description=Tangle Validator Node
After=network-online.target
StartLimitIntervalSec=0
[Service]
User=$USER
Restart=always
RestartSec=3
LimitNOFILE=65535
ExecStart=/usr/bin/tangle \
  --base-path $HOME/.tangle/data/ \
  --name <change-yourname> \
  --chain $HOME/.tangle/tangle-standalone.json \
  --node-key-file "$HOME/.tangle/node-key" \
  --port 30333 \
  --rpc-port 9933 \
  --prometheus-port 9615 \
  --auto-insert-keys \
  --validator \
  --telemetry-url "wss://telemetry.polkadot.io/submit 0" \
  --no-mdns
[Install]
WantedBy=multi-user.target
EOF
```

Edit `<change-yourname>` to what is do you want!!!

# Enable the services
```
systemctl daemon-reload
systemctl enable tangle
systemctl start tangle
```
# Checking logs
```
journalctl -u tangle -f -o cat
```
```
2023-03-22 14:55:51 Tangle Standalone Node
2023-03-22 14:55:51 ✌️  version 0.1.15-54624e3-aarch64-macos
2023-03-22 14:55:51 ❤️  by Webb Technologies Inc., 2017-2023
2023-03-22 14:55:51 📋 Chain specification: Tangle Testnet
2023-03-22 14:55:51 🏷  Node name: 00xPTK
2023-03-22 14:55:51 👤 Role: FULL
2023-03-22 14:55:51 💾 Database: RocksDb at /Users/local/Library/Application Support/tangle-standalone/chains/local_testnet/db/full
2023-03-22 14:55:51 ⛓  Native runtime: tangle-standalone-115 (tangle-standalone-1.tx1.au1)
2023-03-22 14:55:51 Bn254 x5 w3 params
2023-03-22 14:55:51 [0] 💸 generated 5 npos voters, 5 from validators and 0 nominators
2023-03-22 14:55:51 [0] 💸 generated 5 npos targets
2023-03-22 14:55:51 [0] 💸 generated 5 npos voters, 5 from validators and 0 nominators
2023-03-22 14:55:51 [0] 💸 generated 5 npos targets
2023-03-22 14:55:51 [0] 💸 new validator set of size 5 has been processed for era 1
2023-03-22 14:55:52 🔨 Initializing Genesis block/state (state: 0xfd16…aefd, header-hash: 0x7c05…a27d)
2023-03-22 14:55:52 👴 Loading GRANDPA authority set from genesis on what appears to be first startup.
2023-03-22 14:55:53 Using default protocol ID "sup" because none is configured in the chain specs
2023-03-22 14:55:53 🏷  Local node identity is: 12D3KooWDaeXbqokqvEMqpJsKBvjt9BUz41uP9tzRkYuky1Wat7Z
2023-03-22 14:55:53 💻 Operating system: macos
2023-03-22 14:55:53 💻 CPU architecture: aarch64
2023-03-22 14:55:53 📦 Highest known block at #0
2023-03-22 14:55:53 〽️ Prometheus exporter started at 127.0.0.1:9615
2023-03-22 14:55:53 Running JSON-RPC HTTP server: addr=127.0.0.1:9933, allowed origins=["http://localhost:*", "http://127.0.0.1:*", "https://localhost:*", "https://127.0.0.1:*", "https://polkadot.js.org"]
2023-03-22 14:55:53 Running JSON-RPC WS server: addr=127.0.0.1:9944, allowed origins=["http://localhost:*", "http://127.0.0.1:*", "https://localhost:*", "https://127.0.0.1:*", "https://polkadot.js.org"]
2023-03-22 14:55:53 discovered: 12D3KooWMr4L3Dun4BUyp23HZtLfxoQjR56dDp9eH42Va5X6Hfgi /ip4/192.168.0.125/tcp/30304
2023-03-22 14:55:53 discovered: 12D3KooWNHhcCUsZTdTkADmDJbSK9YjbtscHHA8R4jvrbGwjPVez /ip4/192.168.0.125/tcp/30305
2023-03-22 14:55:53 discovered: 12D3KooWMr4L3Dun4BUyp23HZtLfxoQjR56dDp9eH42Va5X6Hfgi /ip4/192.168.88.12/tcp/30304
2023-03-22 14:55:53 discovered: 12D3KooWNHhcCUsZTdTkADmDJbSK9YjbtscHHA8R4jvrbGwjPVez /ip4/192.168.88.12/tcp/30305
```
Checking your node status in [**telemetry**](https://telemetry.polkadot.io/#list/0xea63e6ac7da8699520af7fb540470d63e48eccb33f7273d2e21a935685bf1320)
