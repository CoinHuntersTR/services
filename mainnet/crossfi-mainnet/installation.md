# Installation

| Chain ID           | Latest Version Tag | Custom Port |
| ------------------ | ------------------ | ----------- |
| mineplex-mainnet-1 | v0.1.1             | 26          |

#### Install dependencies <a href="#install-dependencies" id="install-dependencies"></a>

**UPDATE SYSTEM AND INSTALL BUILD TOOLS**

```
sudo apt update && sudo apt upgrade -y
sudo apt install curl git wget htop tmux build-essential jq make lz4 gcc unzip -y
```

**INSTALL GO**

>

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
echo "export CROSSFI_CHAIN_ID="mineplex-mainnet-1"" >> $HOME/.bash_profile
echo "export CROSSFI_PORT="26"" >> $HOME/.bash_profile
source $HOME/.bash_profile
```

#### Download and build binaries <a href="#download-and-build-binaries" id="download-and-build-binaries"></a>

```
cd $HOME
wget https://github.com/crossfichain/crossfi-node/releases/download/v0.1.1/mineplex-2-node._v0.1.1_linux_amd64.tar.gz && tar -xf mineplex-2-node._v0.1.1_linux_amd64.tar.gz
tar -xvf mineplex-2-node._v0.1.1_linux_amd64.tar.gz
chmod +x $HOME/mineplex-chaind
mv $HOME/mineplex-chaind $HOME/go/bin/crossfid
rm mineplex-2-node._v0.1.1_linux_amd64.tar.gz
```

#### Config init app

```
rm -rf testnet ~/.mineplex-chain
git clone https://github.com/crossfichain/mainnet.git
mv $HOME/mainnet/ $HOME/.mineplex-chain/
sed -i '99,114 s/^\( *enable =\).*/\1 "false"/' $HOME/.mineplex-chain/config/config.toml
```

#### Download Genesis and Addrbook

```
wget -O $HOME/.mineplex-chain/config/genesis.json https://mainnet-files.itrocket.net/crossfi/genesis.json
wget -O $HOME/.mineplex-chain/config/addrbook.json https://mainnet-files.itrocket.net/crossfi/addrbook.json
```

#### Set seeds and peers

```
SEEDS="693d9fe729d41ade244717176ab1415b2c06cf86@crossfi-mainnet-seed.itrocket.net:48656"
PEERS="641157ecbfec8e0ec37ca4c411c1208ca1327154@crossfi-mainnet-peer.itrocket.net:11656,e9e9b88a8d44eef023ae2109d9aa2a9f0e0fea52@65.108.75.96:16656,0ff5345e27de5543b80916fb135da92450b0d8c0@65.108.45.174:37656,20671548446b36dda5f9d831e754ae46dd6fda52@116.202.160.22:26656,2d409d9d724364608978d11a3975c7556c813ffc@188.246.224.43:26656,9b500d3f67c22a5a5e5cff6c8db10bf947dbea95@13.231.218.123:26656,d12f9642a604cbcf1afa85608179f49259709f3e@135.181.178.119:22656,1f3de9b4e3764cbcc90f38947bf6149174101c2e@65.108.225.207:50656,bdeae6e8f7cffb7314aca22a26be64115f68b2fc@46.149.79.113:26656,2100a709a14708aec10eb4ac5111675ae11698f9@167.99.252.56:26656,30cda81b201ea9dd7460f75e22aad9da03e181e3@52.199.135.172:26656"
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.mineplex-chain/config/config.toml
```

#### Config Pruning

```
sed -i -e "s/^pruning *=.*/pruning = \"custom\"/" $HOME/.mineplex-chain/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"100\"/" $HOME/.mineplex-chain/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"50\"/" $HOME/.mineplex-chain/config/app.toml
```

#### set minimum gas price, enable prometheus and disable indexing

```
sed -i 's|minimum-gas-prices =.*|minimum-gas-prices = "10000000000000mpx"|g' $HOME/.mineplex-chain/config/app.toml
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.mineplex-chain/config/config.toml
sed -i -e "s/^indexer *=.*/indexer = \"null\"/" $HOME/.mineplex-chain/config/config.toml
```

#### create service file

```
sudo tee /etc/systemd/system/crossfid.service > /dev/null <<EOF
[Unit]
Description=Crossfi node
After=network-online.target
[Service]
User=$USER
WorkingDirectory=$HOME/.mineplex-chain
ExecStart=$(which crossfid) start --home $HOME/.mineplex-chain
Restart=on-failure
RestartSec=5
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF
```

#### Snapshot

```
crossfid tendermint unsafe-reset-all --home $HOME/.mineplex-chain
if curl -s --head curl https://mainnet-files.itrocket.net/crossfi/snap_crossfi.tar.lz4 | head -n 1 | grep "200" > /dev/null; then
  curl https://mainnet-files.itrocket.net/crossfi/snap_crossfi.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.mineplex-chain
    else
  echo no have snap
fi
```

#### enable and start service

```
sudo systemctl daemon-reload
sudo systemctl enable crossfid
sudo systemctl restart crossfid && sudo journalctl -u crossfid -f
```
