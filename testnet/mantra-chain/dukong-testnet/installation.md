# Installation

#### Install dependencies <a href="#install-dependencies" id="install-dependencies"></a>

**UPDATE SYSTEM AND INSTALL BUILD TOOLS**

```
sudo apt update && sudo apt upgrade -y
sudo apt install curl tar wget clang pkg-config libssl-dev jq build-essential bsdmainutils git make ncdu gcc git jq chrony liblz4-tool -y
```

**INSTALL GO**

```
cd $HOME
VER="1.23.2"
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
echo "export MANTRA_CHAIN_ID="mantra-dukong-1"" >> $HOME/.bash_profile
echo "export MANTRA_PORT="22"" >> $HOME/.bash_profile
source $HOME/.bash_profile
```

#### Download and build binaries <a href="#download-and-build-binaries" id="download-and-build-binaries"></a>

```
cd $HOME
wget -O mantrachaind-1.0.0-rc2-linux-amd64.tar.gz https://github.com/MANTRA-Chain/mantrachain/releases/download/v1.0.0-rc2/mantrachaind-1.0.0-rc2-linux-amd64.tar.gz
tar -xzf mantrachaind-1.0.0-rc2-linux-amd64.tar.gz
rm $HOME/mantrachaind-1.0.0-rc2-linux-amd64.tar.gz
chmod +x $HOME/mantrachaind
sudo mv $HOME/mantrachaind $HOME/go/bin/mantrachaind
```

#### Config init app

```
mantrachaind config node tcp://localhost:${MANTRA_PORT}657
mantrachaind config keyring-backend os
mantrachaind config chain-id mantra-dukong-1
mantrachaind init "test" --chain-id mantra-dukong-1
```

#### Download Genesis and Addrbook

```
wget -O $HOME/.mantrachain/config/genesis.json https://raw.githubusercontent.com/CoinHuntersTR/props/refs/heads/main/mantra/dukong/genesis.json
wget -O $HOME/.mantrachain/config/addrbook.json https://raw.githubusercontent.com/CoinHuntersTR/props/refs/heads/main/mantra/dukong/addrbook.json
```

#### Set seeds and peers

```
SEEDS="a9a71700397ce950a9396421877196ac19e7cde0@mantra-testnet-seed.itrocket.net:22656"
PEERS="1a46b1db53d1ff3dbec56ec93269f6a0d15faeb4@mantra-testnet-peer.itrocket.net:22656,ccd9c19b78e4a4075bd228b6d6d534f8c4fd54da@167.235.14.117:26656,df5362cb44533cb3f65c67075db214ccf154c568@37.60.225.54:11656,6414bdede0cfe6c8d6d522db8ec2ff062b066e94@213.199.48.74:23656,33cf22311a510b01552fb1e323add74c641f01c5@65.109.62.39:18656,a209274985b1babe4eabaeb7d1dce17d233e9878@116.202.162.188:17656,2a1aec89ce97fe3c9481e2183987dc899278af31@65.109.92.241:19656,ec6dc74e24168ce92b935d1c2fbc0c9cc4a1ece0@207.180.206.3:23656,e61da86554ee901069b4f5ef07d6996e7dd420f0@84.247.160.249:23656,28dcca0ba822cc7a99ec5390da81d2f1bc9746a8@81.31.197.120:16656,0fd1df61898634c63dfec58cbd310a9b4246958f@138.201.51.154:21104,ad87a59bf9796980c3f78ffc60120c13dd8cfb83@66.45.251.114:13656,30235fa097d100a14d2b534fdbf67e34e8d5f6cf@139.45.205.60:21656,4ccf2fb06244e8b39e3cb28a04602f1c4c593344@37.60.245.125:16656,e430e2c8402f48fb50f8854861c44ae7e2011fe6@217.76.51.182:13656,355ce8ff02bf75ae7418e24d7a60a7f97eeb204f@142.132.180.35:23656,ec579fd3a9563aae863e9bfef783e41fefdaf97b@158.220.112.78:23656,2118a36f67e5c83ca7e4271830073eb3b63342f6@15.235.212.150:56266"
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.mantrachain/config/config.toml
```

#### Config Pruning

```
pruning="custom"
pruning_keep_recent="1000"
pruning_keep_every="0"
pruning_interval="10"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.mantrachain/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.mantrachain/config/app.toml
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.mantrachain/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.mantrachain/config/app.toml
```

#### set minimum gas price, enable prometheus and disable indexing

```
sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.0002uom\"/;" ~/.mantrachain/config/app.toml
external_address=$(wget -qO- eth0.me) 
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:26656\"/" $HOME/.mantrachain/config/config.toml
peers=""
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.mantrachain/config/config.toml
seeds=""
sed -i.bak -e "s/^seeds =.*/seeds = \"$seeds\"/" $HOME/.mantrachain/config/config.toml
sed -i 's/max_num_inbound_peers =.*/max_num_inbound_peers = 50/g' $HOME/.mantrachain/config/config.toml
sed -i 's/max_num_outbound_peers =.*/max_num_outbound_peers = 50/g' $HOME/.mantrachain/config/config.toml
```

#### create service file

```
sudo tee /etc/systemd/system/mantrachaind.service > /dev/null <<EOF
[Unit]
Description=mantrachaind
After=network-online.target

[Service]
User=$USER
ExecStart=$(which mantrachaind) start
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```

#### enable and start service

```
sudo systemctl daemon-reload
sudo systemctl enable mantrachaind
sudo systemctl restart mantrachaind && sudo journalctl -fu mantrachaind -o cat
```

#### Snapshot

#### Stop the service and reset the data <a href="#stop-the-service-and-reset-the-data" id="stop-the-service-and-reset-the-data"></a>

```
sudo systemctl stop mantrachaind
cp $HOME/.mantrachain/data/priv_validator_state.json $HOME/.mantrachain/priv_validator_state.json.backup
rm -rf $HOME/.mantrachain/data
```

#### Download latest snapshot <a href="#download-latest-snapshot" id="download-latest-snapshot"></a>

```
curl -L https://snapshots.coinhunterstr.com/snap_mantra.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.mantrachain
mv $HOME/.mantrachain/priv_validator_state.json.backup $HOME/.mantrachain/data/priv_validator_state.json
```

#### Restart the service and check the log <a href="#restart-the-service-and-check-the-log" id="restart-the-service-and-check-the-log"></a>

```
sudo systemctl restart mantrachaind && sudo journalctl -u mantrachaind-f --no-hostname -o cat
```
