# Installation

| Chain ID       | Latest Version Tag | Custom Port |
| -------------- | ------------------ | ----------- |
| side-testnet-3 | v0.7.0             | 174         |

#### Setup validator name <a href="#setup-validator-name" id="setup-validator-name"></a>

Replace **YOUR\_MONIKER\_GOES\_HERE** with your validator name

```
MONIKER="YOUR_MONIKER_GOES_HERE"
```

#### Install dependencies <a href="#install-dependencies" id="install-dependencies"></a>

**UPDATE SYSTEM AND INSTALL BUILD TOOLS**

```
sudo apt -q update
sudo apt -qy install curl git jq lz4 build-essential
sudo apt -qy upgrade
```

**INSTALL GO**

```
sudo rm -rf /usr/local/go
curl -Ls https://go.dev/dl/go1.21.9.linux-amd64.tar.gz | sudo tar -xzf - -C /usr/local
eval $(echo 'export PATH=$PATH:/usr/local/go/bin' | sudo tee /etc/profile.d/golang.sh)
eval $(echo 'export PATH=$PATH:$HOME/go/bin' | tee -a $HOME/.profile)
```

#### Download and build binaries <a href="#download-and-build-binaries" id="download-and-build-binaries"></a>

```
# Clone project repository
cd $HOME
rm -rf sidechain
git clone https://github.com/sideprotocol/sidechain.git
cd sidechain
git checkout v0.9.4

# Build binaries
make build

# Prepare binaries for Cosmovisor
mkdir -p $HOME/.side/cosmovisor/genesis/bin
mv build/sided $HOME/.side/cosmovisor/genesis/bin/
rm -rf build

# Create application symlinks
sudo ln -s $HOME/.side/cosmovisor/genesis $HOME/.side/cosmovisor/current -f
sudo ln -s $HOME/.side/cosmovisor/current/bin/sided /usr/local/bin/sided -f
```

#### Install Cosmovisor and create a service <a href="#install-cosmovisor-and-create-a-service" id="install-cosmovisor-and-create-a-service"></a>

```
# Download and install Cosmovisor
go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@v1.5.0

# Create service
sudo tee /etc/systemd/system/side.service > /dev/null << EOF
[Unit]
Description=side node service
After=network-online.target

[Service]
User=$USER
ExecStart=$(which cosmovisor) run start
Restart=on-failure
RestartSec=10
LimitNOFILE=65535
Environment="DAEMON_HOME=$HOME/.side"
Environment="DAEMON_NAME=sided"
Environment="UNSAFE_SKIP_BACKUP=true"
Environment="PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:$HOME/.side/cosmovisor/current/bin"

[Install]
WantedBy=multi-user.target
EOF
sudo systemctl daemon-reload
sudo systemctl enable sided
```

#### Initialize the node <a href="#initialize-the-node" id="initialize-the-node"></a>

```
# Set node configuration
sided config chain-id side-testnet-4
sided config keyring-backend test
sided config node tcp://localhost:17457

# Initialize the node
sided init $MONIKER --chain-id side-testnet-4

# Download genesis and addrbook
curl -Ls https://snapshots.kjnodes.com/side-testnet/genesis.json > $HOME/.side/config/genesis.json
curl -Ls https://snapshots.kjnodes.com/side-testnet/addrbook.json > $HOME/.side/config/addrbook.json

# Add seeds
sed -i -e "s|^seeds *=.*|seeds = \"3f472746f46493309650e5a033076689996c8881@side-testnet.rpc.kjnodes.com:17459\"|" $HOME/.side/config/config.toml

# Set minimum gas price
sed -i -e "s|^minimum-gas-prices *=.*|minimum-gas-prices = \"0.005uside\"|" $HOME/.side/config/app.toml

# Set pruning
sed -i \
  -e 's|^pruning *=.*|pruning = "custom"|' \
  -e 's|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|' \
  -e 's|^pruning-keep-every *=.*|pruning-keep-every = "0"|' \
  -e 's|^pruning-interval *=.*|pruning-interval = "19"|' \
  $HOME/.side/config/app.toml

# Set custom ports
sed -i -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:17458\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:17457\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:17460\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:17456\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":17466\"%" $HOME/.side/config/config.toml
sed -i -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:17417\"%; s%^address = \":8080\"%address = \":17480\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:17490\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:17491\"%; s%:8545%:17445%; s%:8546%:17446%; s%:6065%:17465%" $HOME/.side/config/app.toml
```

#### Download latest chain snapshot <a href="#download-latest-chain-snapshot" id="download-latest-chain-snapshot"></a>

```
curl -L https://snapshots.coinhunterstr.com/testnet/side/snapshot_latest.tar.lz4 | tar -Ilz4 -xf - -C $HOME/.side
[[ -f $HOME/.side/data/upgrade-info.json ]] && cp $HOME/.side/data/upgrade-info.json $HOME/.side/cosmovisor/genesis/upgrade-info.json
```

#### Start service and check the logs <a href="#start-service-and-check-the-logs" id="start-service-and-check-the-logs"></a>

```
sudo systemctl start sided && sudo journalctl -u sided -f --no-hostname -o cat
```
