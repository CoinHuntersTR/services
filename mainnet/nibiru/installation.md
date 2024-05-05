# Installation

| Chain ID    | Latest Version Tag | Custom Port |
| ----------- | ------------------ | ----------- |
| cataclysm-1 | v1.2.0             | 139         |

#### Setup validator name <a href="#setup-validator-name" id="setup-validator-name"></a>

Replace **MONIKERNAME** with your validator name

```
MONIKER="MONIKERNAME"
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
rm -rf nibiru
git clone https://github.com/NibiruChain/nibiru.git
cd nibiru
git checkout v1.2.0

# Build binaries
make build

# Prepare binaries for Cosmovisor
mkdir -p $HOME/.nibid/cosmovisor/genesis/bin
mv build/nibid $HOME/.nibid/cosmovisor/genesis/bin/
rm -rf build

# Create application symlinks
sudo ln -s $HOME/.nibid/cosmovisor/genesis $HOME/.nibid/cosmovisor/current -f
sudo ln -s $HOME/.nibid/cosmovisor/current/bin/nibid /usr/local/bin/nibid -f
```

#### Install Cosmovisor and create a service <a href="#install-cosmovisor-and-create-a-service" id="install-cosmovisor-and-create-a-service"></a>

```
# Download and install Cosmovisor
go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@v1.5.0

# Create service
sudo tee /etc/systemd/system/nibiru.service > /dev/null << EOF
[Unit]
Description=nibiru node service
After=network-online.target

[Service]
User=$USER
ExecStart=$(which cosmovisor) run start
Restart=on-failure
RestartSec=10
LimitNOFILE=65535
Environment="DAEMON_HOME=$HOME/.nibid"
Environment="DAEMON_NAME=nibid"
Environment="UNSAFE_SKIP_BACKUP=true"
Environment="PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:$HOME/.nibid/cosmovisor/current/bin"

[Install]
WantedBy=multi-user.target
EOF
sudo systemctl daemon-reload
sudo systemctl enable nibiru.service
```

#### Initialize the node <a href="#initialize-the-node" id="initialize-the-node"></a>

```
# Set node configuration
nibid config chain-id cataclysm-1
nibid config keyring-backend file
nibid config node tcp://localhost:13957

# Initialize the node
nibid init $MONIKER --chain-id cataclysm-1

# Download genesis and addrbook
curl -Ls https://snapshots.kjnodes.com/nibiru/genesis.json > $HOME/.nibid/config/genesis.json
curl -Ls https://snapshots.kjnodes.com/nibiru/addrbook.json > $HOME/.nibid/config/addrbook.json

# Add seeds
sed -i -e "s|^seeds *=.*|seeds = \"400f3d9e30b69e78a7fb891f60d76fa3c73f0ecc@nibiru.rpc.kjnodes.com:13959\"|" $HOME/.nibid/config/config.toml

# Set minimum gas price
sed -i -e "s|^minimum-gas-prices *=.*|minimum-gas-prices = \"0.025unibi\"|" $HOME/.nibid/config/app.toml

# Set pruning
sed -i \
  -e 's|^pruning *=.*|pruning = "custom"|' \
  -e 's|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|' \
  -e 's|^pruning-keep-every *=.*|pruning-keep-every = "0"|' \
  -e 's|^pruning-interval *=.*|pruning-interval = "19"|' \
  $HOME/.nibid/config/app.toml

# Set custom ports
sed -i -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:13958\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:13957\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:13960\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:13956\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":13966\"%" $HOME/.nibid/config/config.toml
sed -i -e "s%^address = \"tcp://localhost:1317\"%address = \"tcp://0.0.0.0:13917\"%; s%^address = \":8080\"%address = \":13980\"%; s%^address = \"localhost:9090\"%address = \"0.0.0.0:13990\"%; s%^address = \"localhost:9091\"%address = \"0.0.0.0:13991\"%; s%:8545%:13945%; s%:8546%:13946%; s%:6065%:13965%" $HOME/.nibid/config/app.toml
```

#### Download latest chain snapshot <a href="#download-latest-chain-snapshot" id="download-latest-chain-snapshot"></a>

```
curl -L https://snapshots.kjnodes.com/nibiru/snapshot_latest.tar.lz4 | tar -Ilz4 -xf - -C $HOME/.nibid
[[ -f $HOME/.nibid/data/upgrade-info.json ]] && cp $HOME/.nibid/data/upgrade-info.json $HOME/.nibid/cosmovisor/genesis/upgrade-info.json
```

#### Start service and check the logs <a href="#start-service-and-check-the-logs" id="start-service-and-check-the-logs"></a>

```
sudo systemctl start nibiru.service && sudo journalctl -u nibiru.service -f --no-hostname -o cat
```
