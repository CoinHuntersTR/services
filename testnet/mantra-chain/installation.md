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
echo "export MANTRA_CHAIN_ID="mantra-hongbai-1"" >> $HOME/.bash_profile
echo "export MANTRA_PORT="22"" >> $HOME/.bash_profile
source $HOME/.bash_profile
```

#### Download and build binaries <a href="#download-and-build-binaries" id="download-and-build-binaries"></a>

```
cd $HOME
sudo wget -O /usr/lib/libwasmvm.x86_64.so https://github.com/CosmWasm/wasmvm/releases/download/v1.3.1/libwasmvm.x86_64.so
wget https://github.com/MANTRA-Finance/public/raw/main/mantrachain-hongbai/mantrachaind-linux-amd64.zip
unzip mantrachaind-linux-amd64.zip
rm mantrachaind-linux-amd64.zip
mv mantrachaind $HOME/go/bin
```

#### Config init app

```
mantrachaind config node tcp://localhost:${MANTRA_PORT}657
mantrachaind config keyring-backend os
mantrachaind config chain-id mantra-hongbai-1
mantrachaind init "test" --chain-id mantra-hongbai-1
```

#### Download Genesis and Addrbook

```
wget -O $HOME/.mantrachain/config/genesis.json https://testnet-files.itrocket.net/mantra/genesis.json
wget -O $HOME/.mantrachain/config/addrbook.json https://testnet-files.itrocket.net/mantra/addrbook.json
```

#### Set seeds and peers

```
SEEDS="a9a71700397ce950a9396421877196ac19e7cde0@mantra-testnet-seed.itrocket.net:22656"
PEERS="1a46b1db53d1ff3dbec56ec93269f6a0d15faeb4@mantra-testnet-peer.itrocket.net:22656,ccd9c19b78e4a4075bd228b6d6d534f8c4fd54da@167.235.14.117:26656,df5362cb44533cb3f65c67075db214ccf154c568@37.60.225.54:11656,6414bdede0cfe6c8d6d522db8ec2ff062b066e94@213.199.48.74:23656,33cf22311a510b01552fb1e323add74c641f01c5@65.109.62.39:18656,a209274985b1babe4eabaeb7d1dce17d233e9878@116.202.162.188:17656,2a1aec89ce97fe3c9481e2183987dc899278af31@65.109.92.241:19656,ec6dc74e24168ce92b935d1c2fbc0c9cc4a1ece0@207.180.206.3:23656,e61da86554ee901069b4f5ef07d6996e7dd420f0@84.247.160.249:23656,28dcca0ba822cc7a99ec5390da81d2f1bc9746a8@81.31.197.120:16656,0fd1df61898634c63dfec58cbd310a9b4246958f@138.201.51.154:21104,ad87a59bf9796980c3f78ffc60120c13dd8cfb83@66.45.251.114:13656,30235fa097d100a14d2b534fdbf67e34e8d5f6cf@139.45.205.60:21656,4ccf2fb06244e8b39e3cb28a04602f1c4c593344@37.60.245.125:16656,e430e2c8402f48fb50f8854861c44ae7e2011fe6@217.76.51.182:13656,355ce8ff02bf75ae7418e24d7a60a7f97eeb204f@142.132.180.35:23656,ec579fd3a9563aae863e9bfef783e41fefdaf97b@158.220.112.78:23656,2118a36f67e5c83ca7e4271830073eb3b63342f6@15.235.212.150:56266"
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.mantrachain/config/config.toml
```

#### Config Pruning

```
sed -i -e "s/^pruning *=.*/pruning = \"custom\"/" $HOME/.mantrachain/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"100\"/" $HOME/.mantrachain/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"50\"/" $HOME/.mantrachain/config/app.toml
```

#### set minimum gas price, enable prometheus and disable indexing

```
sed -i 's|minimum-gas-prices =.*|minimum-gas-prices = "0.0002uom"|g' $HOME/.mantrachain/config/app.toml
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.mantrachain/config/config.toml
sed -i -e "s/^indexer *=.*/indexer = \"null\"/" $HOME/.mantrachain/config/config.toml
```

#### create service file

```
sudo tee /etc/systemd/system/mantrachaind.service > /dev/null <<EOF
[Unit]
Description=Mantra node
After=network-online.target
[Service]
User=$USER
WorkingDirectory=$HOME/.mantrachain
ExecStart=$(which mantrachaind) start --home $HOME/.mantrachain
Restart=on-failure
RestartSec=5
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF
```

#### Snapshot

```
mantrachaind tendermint unsafe-reset-all --home $HOME/.mantrachain
if curl -s --head curl https://testnet-files.itrocket.net/mantra/snap_mantra.tar.lz4 | head -n 1 | grep "200" > /dev/null; then
  curl https://testnet-files.itrocket.net/mantra/snap_mantra.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.mantrachain
    else
  echo no have snap
fi
```

#### enable and start service

```
sudo systemctl daemon-reload
sudo systemctl enable mantrachaind
sudo systemctl restart mantrachaind && sudo journalctl -u mantrachaind -f
```
