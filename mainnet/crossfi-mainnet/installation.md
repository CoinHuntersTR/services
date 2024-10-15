# Installation

| Chain ID           | Latest Version Tag | Custom Port |
| ------------------ | ------------------ | ----------- |
| mineplex-mainnet-1 | v0.3.0             | 26          |

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
echo "export CROSSFI_CHAIN_ID="crossfi-mainnet-1"" >> $HOME/.bash_profile
echo "export CROSSFI_PORT="48"" >> $HOME/.bash_profile
source $HOME/.bash_profile
```

#### Download and build binaries <a href="#download-and-build-binaries" id="download-and-build-binaries"></a>

```
cd $HOME
rm -rf bin
wget https://github.com/crossfichain/crossfi-node/releases/download/v0.3.0/crossfi-node_0.3.0_linux_amd64.tar.gz && tar -xf crossfi-node_0.3.0_linux_amd64.tar.gz
rm crossfi-node_0.3.0_linux_amd64.tar.gz
mv $HOME/bin/crossfid $HOME/go/bin/crossfid
```

#### Config init app

```
rm -rf testnet ~/.mineplex-chain
git clone https://github.com/crossfichain/mainnet.git
mv $HOME/mainnet/ $HOME/.crossfid/
sed -i '99,114 s/^\( *enable =\).*/\1 "false"/' $HOME/.crossfid/config/config.toml
```

#### Download Genesis and Addrbook

```
wget -O $HOME/.crossfid/config/genesis.json https://server-3.itrocket.net/mainnet/crossfi/genesis.json
wget -O $HOME/.crossfid/config/addrbook.json  https://server-3.itrocket.net/mainnet/crossfi/addrbook.json
```

#### Set seeds and peers

```
SEEDS="693d9fe729d41ade244717176ab1415b2c06cf86@crossfi-mainnet-seed.itrocket.net:48656"
PEERS="641157ecbfec8e0ec37ca4c411c1208ca1327154@crossfi-mainnet-peer.itrocket.net:11656,9dd9a718a70c17eda4a2f2e262a6fcdafa380b04@95.217.45.201:23656,c482ab7bb52202149477fded22d6741d746d7e45@95.217.204.58:26056,d996012096cfef860bf24543740d58da45e5b194@37.27.183.62:26656,6b90dd8399533bca9066030f6193dca37f1565e1@65.109.234.80:26656,adb475675d97a9ce67bcea8cfdd66f23b92f1162@89.110.91.158:26656,9c8bf508ead86588f41ecc76cc6021c88493c199@57.129.32.223:26656,f27eff68f2f3542a317bad66fdf9f1cc93a80dc1@49.13.76.170:26656,f8cbc62fb487ae825edf79c580206d0e34ee9f51@5.161.229.160:26656,90fd2ad4f2b57bf6fa0c40cd579310f5ceebf0f5@5.78.128.70:26656,f5d2b1a6ab68ac9357366afe424564ab42a9d444@185.107.82.171:26656"
sed -i -e "/^\[p2p\]/,/^\[/{s/^[[:space:]]*seeds *=.*/seeds = \"$SEEDS\"/}" \
       -e "/^\[p2p\]/,/^\[/{s/^[[:space:]]*persistent_peers *=.*/persistent_peers = \"$PEERS\"/}" $HOME/.crossfid/config/config.toml
```

#### Config Pruning

```
sed -i -e "s/^pruning *=.*/pruning = \"custom\"/" $HOME/.mineplex-chain/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"100\"/" $HOME/.mineplex-chain/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"50\"/" $HOME/.mineplex-chain/config/app.toml
```

#### set minimum gas price, enable prometheus and disable indexing

```
sed -i 's|minimum-gas-prices =.*|minimum-gas-prices = "10000000000000mpx"|g' $HOME/.crossfid/config/app.toml
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.crossfid/config/config.toml
sed -i -e "s/^indexer *=.*/indexer = \"null\"/" $HOME/.crossfid/config/config.toml
```

#### create service file

```
sudo tee /etc/systemd/system/crossfid.service > /dev/null <<EOF
[Unit]
Description=Crossfi node
After=network-online.target
[Service]
User=$USER
WorkingDirectory=$HOME/.crossfid
ExecStart=$(which crossfid) start --home $HOME/.crossfid
Restart=on-failure
RestartSec=5
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF
```

#### Snapshot

```
crossfid tendermint unsafe-reset-all --home $HOME/.crossfid
if curl -s --head curl https://server-3.itrocket.net/mainnet/crossfi/crossfi_2024-10-15_3249_snap.tar.lz4 | head -n 1 | grep "200" > /dev/null; then
  curl https://server-3.itrocket.net/mainnet/crossfi/crossfi_2024-10-15_3249_snap.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.crossfid
    else
  echo "no snapshot founded"
fi
```

#### enable and start service

```
sudo systemctl daemon-reload
sudo systemctl enable crossfid
sudo systemctl restart crossfid && sudo journalctl -u crossfid -f
```
