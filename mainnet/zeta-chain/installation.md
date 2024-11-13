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
VER="1.20.3"
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
echo "export MONIKER="test"" >> $HOME/.bash_profile
echo "export ZETACHAIN_CHAIN_ID="zetachain_7000-1"" >> $HOME/.bash_profile
echo "export ZETACHAIN_PORT="39"" >> $HOME/.bash_profile
source $HOME/.bash_profile
```

#### Download and build binaries <a href="#download-and-build-binaries" id="download-and-build-binaries"></a>

```
cd $HOME
wget -O $HOME/zetacored https://github.com/zeta-chain/node/releases/download/v20.0.2/zetacored-linux-amd64
chmod +x $HOME/zetacored-linux-amd64
mv $HOME/zetacored-linux-amd64 $HOME/go/bin/zetacored
```

#### Config init app

```
zetacored init $MONIKER --chain-id zetachain_7000-1
wget -O $HOME/.zetacored/config/app.toml  https://raw.githubusercontent.com/zeta-chain/network-mainnet/main/network_files/config/app.toml
wget -O $HOME/.zetacored/config/client.toml https://raw.githubusercontent.com/zeta-chain/network-mainnet/main/network_files/config/client.toml
wget -O $HOME/.zetacored/config/config.toml https://raw.githubusercontent.com/zeta-chain/network-mainnet/main/network_files/config/config.toml
wget -O $HOME/.zetacored/config/genesis.json https://raw.githubusercontent.com/zeta-chain/network-mainnet/main/network_files/config/genesis.json
```

#### Download Genesis and Addrbook

```
wget -O $HOME/.zetacored/config/genesis.json https://mainnet-files.itrocket.net/zetachain/genesis.json
wget -O $HOME/.zetacored/config/addrbook.json https://mainnet-files.itrocket.net/zetachain/addrbook.json
```

#### Set seeds and peers

```
SEEDS="4e668be2d80d3475d2350e313bc75b8f0646884f@zetachain-mainnet-seed.itrocket.net:39656"
PEERS="372e9c80f723491daf2b05b3aa368865f6bc3492@zetachain-mainnet-peer.itrocket.net:39656,a5d984f145ea828048a0741a1d349f9fa13a643b@91.210.101.148:26656,bfdd895017abc5c9f0d16f077f4fb40afbd9b8e4@131.153.154.9:26656,f55f191f5036289ce0b7c2aee5aea6a3421e4a1d@51.178.76.16:26656,ae2be7a0c7fa3b8ae777c9e88032cf7b53849cf3@8.222.212.155:26656,d98525ae59a00f7a099ddaec2a7e416e818bb210@57.128.141.153:26656,b855a804a4caf334bc74794b48608b39f05f58d5@212.126.35.133:26656,6554807a10fd2d0e34b8daeed3fee38b8ca048b6@51.91.214.146:26656,97823ae690d164453b775c53ba956e2ea1b55d1d@65.21.32.200:31656,d2579f98938f428b90619eadba01b4342a2d19bd@35.215.48.36:26656,9ffffe23d7e50bab086dd86982b06c65e56e0b30@35.210.154.98:26656"
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.zetacored/config/config.toml
```

#### Config Pruning

```
sed -i -e "s/^pruning *=.*/pruning = \"custom\"/" $HOME/.zetacored/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"100\"/" $HOME/.zetacored/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"50\"/" $HOME/.zetacored/config/app.toml
```

#### set minimum gas price, enable prometheus and disable indexing

```
sed -i 's|minimum-gas-prices =.*|minimum-gas-prices = "0.0azeta"|g' $HOME/.zetacored/config/app.toml
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.zetacored/config/config.toml
sed -i -e "s/^indexer *=.*/indexer = \"null\"/" $HOME/.zetacored/config/config.toml
```

#### create service file

```
sudo tee /etc/systemd/system/zetacored.service > /dev/null <<EOF
[Unit]
Description=Zetachain node
After=network-online.target
[Service]
User=$USER
WorkingDirectory=$HOME/.zetacored
ExecStart=$(which zetacored) start --home $HOME/.zetacored
Restart=on-failure
RestartSec=5
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF
```

#### Snapshot

```
zetacored tendermint unsafe-reset-all --home $HOME/.zetacored
if curl -s --head curl https://snapshots.coinhunterstr.com/mainnet/zetachain/snapshot_latest.tar.lz4 | head -n 1 | grep "200" > /dev/null; then
  curl https://snapshots.coinhunterstr.com/mainnet/zetachain/snapshot_latest.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.zetacored
    else
  echo no have snap
fi
```

#### enable and start service

```
sudo systemctl daemon-reload
sudo systemctl enable zetacored
sudo systemctl restart zetacored && sudo journalctl -u zetacored -f
```
