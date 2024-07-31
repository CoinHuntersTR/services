# Installion

#### Recommended Hardware

| CPU    | RAM  | SSD     |
| ------ | ---- | ------- |
| 4 vCPU | 16GB | 200 SSD |

### Manual Installation <a href="#installation" id="installation"></a>

#### Install dependencies <a href="#install-dependencies" id="install-dependencies"></a>

**UPDATE SYSTEM AND INSTALL BUILD TOOLS**

```
sudo apt update && sudo apt upgrade -y
sudo apt install curl git wget htop tmux build-essential jq make lz4 gcc unzip -y
```

**INSTALL GO**

```
cd $HOME
VER="2.2.0"
wget "https://golang.org/dl/go$VER.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$VER.linux-amd64.tar.gz"
rm "go$VER.linux-amd64.tar.gz"
[ ! -f ~/.bash_profile ] && touch ~/.bash_profile
echo "export PATH=$PATH:/usr/local/go/bin:~/go/bin" >> ~/.bash_profile
source $HOME/.bash_profile
[ ! -d ~/go/bin ] && mkdir -p ~/go/bin
```

#### Download and build binaries <a href="#download-and-build-binaries" id="download-and-build-binaries"></a>

> You can enter your validator name instead of `MonikerName`.

```
# Clone project repository
cd $HOME
rm -rf lava
git clone https://github.com/lavanet/lava
cd lava
git checkout v2.2.0
make install-all

# config and init app
lavad init MonikerName --chain-id lava-mainnet-1
sed -i \
-e 's/timeout_propose = .*/timeout_propose = "1s"/' \
-e 's/timeout_propose_delta = .*/timeout_propose_delta = "500ms"/' \
-e 's/timeout_prevote = .*/timeout_prevote = "1s"/' \
-e 's/timeout_prevote_delta = .*/timeout_prevote_delta = "500ms"/' \
-e 's/timeout_precommit = .*/timeout_precommit = "500ms"/' \
-e 's/timeout_precommit_delta = .*/timeout_precommit_delta = "1s"/' \
-e 's/timeout_commit = .*/timeout_commit = "15s"/' \
-e 's/^create_empty_blocks = .*/create_empty_blocks = true/' \
-e 's/^create_empty_blocks_interval = .*/create_empty_blocks_interval = "15s"/' \
-e 's/^timeout_broadcast_tx_commit = .*/timeout_broadcast_tx_commit = "151s"/' \
-e 's/skip_timeout_commit = .*/skip_timeout_commit = false/' \
  $HOME/.lava/config/config.toml
```

#### Download Genesis and Addrbook <a href="#initialize-the-node" id="initialize-the-node"></a>

```
# Download genesis and addrbook
wget -O $HOME/.lava/config/genesis.json https://raw.githubusercontent.com/CoinHuntersTR/props/main/lava-mainnet/genesis.json
wget -O $HOME/.lava/config/addrbook.json  https://raw.githubusercontent.com/CoinHuntersTR/props/main/lava-mainnet/addrbook.json
```

#### Update chain-specific configuration <a href="#update-chain-specific-configuration" id="update-chain-specific-configuration"></a>

```
# config pruning
sed -i -e "s/^pruning *=.*/pruning = \"custom\"/" $HOME/.lava/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"100\"/" $HOME/.lava/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"50\"/" $HOME/.lava/config/app.toml

# set minimum gas price, enable prometheus and disable indexing
sed -i 's|minimum-gas-prices =.*|minimum-gas-prices = "0.000000001ulava"|g' $HOME/.lava/config/app.toml
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.lava/config/config.toml
sed -i -e "s/^indexer *=.*/indexer = \"null\"/" $HOME/.lava/config/config.toml
```

#### Create a service <a href="#install-cosmovisor-and-create-a-service" id="install-cosmovisor-and-create-a-service"></a>

```
sudo tee /etc/systemd/system/lavad.service > /dev/null <<EOF
[Unit]
Description=Lava node
After=network-online.target
[Service]
User=$USER
WorkingDirectory=$HOME/.lava
ExecStart=$(which lavad) start --home $HOME/.lava
Restart=on-failure
RestartSec=5
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF
```

#### Download latest chain snapshot <a href="#download-latest-chain-snapshot" id="download-latest-chain-snapshot"></a>

```
curl -L https://snapshots.coinhunterstr.com/lava/snapshot_latest.tar.lz4 | tar -Ilz4 -xf - -C $HOME/.lava
[[ -f $HOME/.lava/data/upgrade-info.json ]] && cp $HOME/.lava/data/upgrade-info.json $HOME/.lava/cosmovisor/genesis/upgrade-info.json
```

#### Start service and check the logs <a href="#start-service-and-check-the-logs" id="start-service-and-check-the-logs"></a>

```
# enable and start service
sudo systemctl daemon-reload
sudo systemctl enable lavad
sudo systemctl restart lavad && sudo journalctl -u lavad -f
```

### Automatic Installation <a href="#auto-installation" id="auto-installation"></a>

```
wget -q -O lava.sh https://raw.githubusercontent.com/CoinHuntersTR/props/main/AutoInstall/lava.sh && chmod +x lava.sh && ./lava.sh
```

