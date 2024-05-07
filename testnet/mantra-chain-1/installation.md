# Installation

#### Install dependencies <a href="#install-dependencies" id="install-dependencies"></a>

**UPDATE SYSTEM AND INSTALL BUILD TOOLS**

```
sudo apt update && sudo apt upgrade -y
sudo apt install curl git wget htop tmux build-essential jq make lz4 gcc unzip -y
```

**INSTALL GO**

```
cd $HOME
VER="1.21.3"
wget "https://golang.org/dl/go$VER.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$VER.linux-amd64.tar.gz"
rm "go$VER.linux-amd64.tar.gz"
[ ! -f ~/.bash_profile ] && touch ~/.bash_profile
echo "export PATH=$PATH:/usr/local/go/bin:~/go/bin" >> ~/.bash_profile
source $HOME/.bash_profile
[ ! -d ~/go/bin ] && mkdir -p ~/go/bin
```



### Set Vars

WALLET yerine istediğiniz bir ismi, MONIKER yerine bir validator adı yazmayı unutmayın.&#x20;

```
echo "export WALLET="wallet"" >> $HOME/.bash_profile
echo "export MONIKER="test"" >> $HOME/.bash_profile
echo "export OG_CHAIN_ID="zgtendermint_9000-1"" >> $HOME/.bash_profile
echo "export OG_PORT="47"" >> $HOME/.bash_profile
source $HOME/.bash_profile
```

#### Download and build binaries <a href="#download-and-build-binaries" id="download-and-build-binaries"></a>

```
rm -rf 0g-evmos
git clone https://github.com/0glabs/0g-evmos.git
cd 0g-evmos
git checkout v1.0.0-testnet
make build
mv $HOME/0g-evmos/build/evmosd $HOME/go/bin/
```

#### Config init app

```
evmosd config node tcp://localhost:${OG_PORT}657
evmosd config keyring-backend os
evmosd config chain-id zgtendermint_9000-1
evmosd init "test" --chain-id zgtendermint_9000-1
```

#### Download Genesis and Addrbook

```
wget -O $HOME/.evmosd/config/genesis.json https://testnet-files.itrocket.net/og/genesis.json
wget -O $HOME/.evmosd/config/addrbook.json https://testnet-files.itrocket.net/og/addrbook.json
```

#### Set seeds and peers

```
SEEDS="c9b8e7e220178817c84c7268e186b231bc943671@og-testnet-seed.itrocket.net:47656"
PEERS=""
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.evmosd/config/config.toml
```

#### Config Pruning

```
sed -i -e "s/^pruning *=.*/pruning = \"custom\"/" $HOME/.evmosd/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"100\"/" $HOME/.evmosd/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"50\"/" $HOME/.evmosd/config/app.toml
```

#### set minimum gas price, enable prometheus and disable indexing

```
sed -i 's|minimum-gas-prices =.*|minimum-gas-prices = "0agnet"|g' $HOME/.evmosd/config/app.toml
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.evmosd/config/config.toml
sed -i -e "s/^indexer *=.*/indexer = \"null\"/" $HOME/.evmosd/config/config.toml
```

#### create service file

```
sudo tee /etc/systemd/system/evmosd.service > /dev/null <<EOF
[Unit]
Description=Og node
After=network-online.target
[Service]
User=$USER
WorkingDirectory=$HOME/.evmosd
ExecStart=$(which evmosd) start --home $HOME/.evmosd
Restart=on-failure
RestartSec=5
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF
```

#### Snapshot

```
evmosd tendermint unsafe-reset-all --home $HOME/.evmosd
if curl -s --head curl https://testnet-files.itrocket.net/og/snap_og.tar.lz4 | head -n 1 | grep "200" > /dev/null; then
  curl https://testnet-files.itrocket.net/og/snap_og.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.evmosd
    else
  echo no have snap
fi
```

#### enable and start service

```
sudo systemctl daemon-reload
sudo systemctl enable evmosd
sudo systemctl restart evmosd && sudo journalctl -u evmosd -f
```
