# Installation

| Chain ID          | Latest Version Tag | Custom Port |
| ----------------- | ------------------ | ----------- |
| dymension\_1100-1 | v3.1.0             | 146         |

#### Setup validator name <a href="#setup-validator-name" id="setup-validator-name"></a>

Replace **MONIKERNAME** with your validator name

```markup
MONIKER="MONIKERNAME"
```

#### Install dependencies <a href="#install-dependencies" id="install-dependencies"></a>

**UPDATE SYSTEM AND INSTALL BUILD TOOLS**

```markup
sudo apt -q update
sudo apt -qy install curl git jq lz4 build-essential
sudo apt -qy upgrade
```

**INSTALL GO**

```markup
sudo rm -rf /usr/local/go
curl -Ls https://go.dev/dl/go1.21.9.linux-amd64.tar.gz | sudo tar -xzf - -C /usr/local
eval $(echo 'export PATH=$PATH:/usr/local/go/bin' | sudo tee /etc/profile.d/golang.sh)
eval $(echo 'export PATH=$PATH:$HOME/go/bin' | tee -a $HOME/.profile)
```

#### Download and build binaries <a href="#download-and-build-binaries" id="download-and-build-binaries"></a>

```markup
# Clone project repository
cd $HOME
rm -rf dymension
git clone https://github.com/dymensionxyz/dymension.git
cd dymension
git checkout v3.1.0

# Build binaries
make build

# Prepare binaries for Cosmovisor
mkdir -p $HOME/.dymension/cosmovisor/genesis/bin
mv build/dymd $HOME/.dymension/cosmovisor/genesis/bin/
rm -rf build

# Create application symlinks
sudo ln -s $HOME/.dymension/cosmovisor/genesis $HOME/.dymension/cosmovisor/current -f
sudo ln -s $HOME/.dymension/cosmovisor/current/bin/dymd /usr/local/bin/dymd -f
```

#### Install Cosmovisor and create a service <a href="#install-cosmovisor-and-create-a-service" id="install-cosmovisor-and-create-a-service"></a>

```markup
# Download and install Cosmovisor
go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@v1.5.0

# Create service
sudo tee /etc/systemd/system/dymension.service > /dev/null << EOF
[Unit]
Description=dymension node service
After=network-online.target

[Service]
User=$USER
ExecStart=$(which cosmovisor) run start
Restart=on-failure
RestartSec=10
LimitNOFILE=65535
Environment="DAEMON_HOME=$HOME/.dymension"
Environment="DAEMON_NAME=dymd"
Environment="UNSAFE_SKIP_BACKUP=true"
Environment="PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:$HOME/.dymension/cosmovisor/current/bin"

[Install]
WantedBy=multi-user.target
EOF
sudo systemctl daemon-reload
sudo systemctl enable dymension.service
```

#### Initialize the node <a href="#initialize-the-node" id="initialize-the-node"></a>

```markup
# Set node configuration
dymd config chain-id dymension_1100-1
dymd config keyring-backend file
dymd config node tcp://localhost:14657

# Initialize the node
dymd init $MONIKER --chain-id dymension_1100-1

# Download genesis and addrbook
curl -Ls https://snapshots.coinhunterstr.com/dymension/genesis.json > $HOME/.dymension/config/genesis.json
curl -Ls https://raw.githubusercontent.com/CoinHuntersTR/props/main/dymension/addrbook.json > $HOME/.dymension/config/addrbook.json

# Add seeds
sed -i -e "s|^seeds *=.*|seeds = \"45be427b2fc01810597991f5bacdc091a60e2fc4@167.235.192.220:14656\"|" $HOME/.dymension/config/config.toml

# Set minimum gas price
sed -i -e "s|^minimum-gas-prices *=.*|minimum-gas-prices = \"20000000000adym\"|" $HOME/.dymension/config/app.toml

# Set pruning
sed -i \
  -e 's|^pruning *=.*|pruning = "custom"|' \
  -e 's|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|' \
  -e 's|^pruning-keep-every *=.*|pruning-keep-every = "0"|' \
  -e 's|^pruning-interval *=.*|pruning-interval = "19"|' \
  $HOME/.dymension/config/app.toml

# Set custom ports
sed -i -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:14658\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:14657\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:14660\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:14656\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":14666\"%" $HOME/.dymension/config/config.toml
sed -i -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:14617\"%; s%^address = \":8080\"%address = \":14680\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:14690\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:14691\"%; s%:8545%:14645%; s%:8546%:14646%; s%:6065%:14665%" $HOME/.dymension/config/app.toml
```

#### Download latest chain snapshot <a href="#download-latest-chain-snapshot" id="download-latest-chain-snapshot"></a>

```markup
curl -L https://snapshots.coinhunterstr.com/dymension/snapshot_latest.tar.lz4 | tar -Ilz4 -xf - -C $HOME/.dymension
[[ -f $HOME/.dymension/data/upgrade-info.json ]] && cp $HOME/.dymension/data/upgrade-info.json $HOME/.dymension/cosmovisor/genesis/upgrade-info.json
```

#### Start service and check the logs <a href="#start-service-and-check-the-logs" id="start-service-and-check-the-logs"></a>

```
sudo systemctl start dymension.service && sudo journalctl -u dymension.service -f --no-hostname -o cat
```
