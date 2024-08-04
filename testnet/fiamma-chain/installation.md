# Installation

### Manual Installation <a href="#installation" id="installation"></a>

#### Gerekli Sistem <a href="#install-dependencies" id="install-dependencies"></a>

Ubuntu 22.04

<table><thead><tr><th width="279">CPU</th><th>RAM</th><th>SSD</th></tr></thead><tbody><tr><td>4 vCPU</td><td>8 GB RAM</td><td>160 SSD</td></tr></tbody></table>

#### Install dependencies <a href="#install-dependencies" id="install-dependencies"></a>

**UPDATE SYSTEM AND INSTALL BUILD TOOLS**

```
sudo apt update && sudo apt upgrade -y
sudo apt install curl git wget htop tmux build-essential jq make lz4 gcc unzip -y
sudo apt-get install -y libssl-dev
```

**INSTALL GO**

```
ver="1.22.3" 
cd $HOME 
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz" 

sudo rm -rf /usr/local/go 
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz" 
rm "go$ver.linux-amd64.tar.gz"

echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile
source $HOME/.bash_profile    
```

#### Download and build binaries <a href="#download-and-build-binaries" id="download-and-build-binaries"></a>

```
cd $HOME
rm -rf fiamma
git clone https://github.com/fiamma-chain/fiamma
cd fiamma
git checkout v0.1.3
make install
```

#### Set Vars

> `Moniker` yerine validator adınızı ekliyoruz.

```
fiammad init $MONIKER --chain-id fiamma-testnet-1
sed -i -e "s|^node *=.*|node = \"tcp://localhost:26657\"|" $HOME/.fiamma/config/client.toml
sed -i -e "s|^keyring-backend *=.*|keyring-backend = \"os\"|" $HOME/.fiamma/config/client.toml
sed -i -e "s|^chain-id *=.*|chain-id = \"fiamma-testnet-1\"|" $HOME/.fiamma/config/client.toml
```

#### Download Genesis and Addrbook

```
wget -O $HOME/.fiamma/config/genesis.json https://raw.githubusercontent.com/CoinHuntersTR/props/main/fiamma/genesis.json
wget -O $HOME/.fiamma/config/addrbook.json  https://raw.githubusercontent.com/CoinHuntersTR/props/main/fiamma/addrbook.json
```

#### Config Pruning

```
sed -i -e "s/^pruning *=.*/pruning = \"custom\"/" $HOME/.fiamma/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"100\"/" $HOME/.fiamma/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"50\"/" $HOME/.fiamma/config/app.toml
sed -i 's|minimum-gas-prices =.*|minimum-gas-prices = "0.0001ufia"|g' $HOME/.fiamma/config/app.toml
```

#### Set seeds and peers

```
SEEDS=""
PEERS="16b7389e724cc440b2f8a2a0f6b4c495851934ff@fiamma-testnet-peer.itrocket.net:49656,74ec322e114b6757ac066a7b6b55cd224cdb8885@65.21.167.216:37656,37e2b149db5558436bd507ecca2f62fe605f92fe@88.198.27.51:60556,e30701492127fdd86ccf243a55b9dc4146772235@213.199.42.85:37656,e2b57b310a6f3c4c0f85fc3dc3447d7e9696cd65@95.165.89.222:26706,421beadda6355465be81703fd8d25c30b2233df0@5.78.71.69:26656,21a5cae23e835f99735798024eef39fa0875bc62@65.109.30.110:17456,dd09c5a54d233d7b1b238eecedf7d855b4cb549c@65.108.81.145:26656,043da1f559e0f83eff52ff65f76b012f0f0ee9b3@198.7.119.198:37656,5a6bdb09c087012e9aa9bbdaa95694a82d489a94@144.76.155.11:26856,a03a1a53fafb669bfcce53b8b2a1362aa153cf99@77.90.13.137:37656"
sed -i -e "/^\[p2p\]/,/^\[/{s/^[[:space:]]*seeds *=.*/seeds = \"$SEEDS\"/}" \
       -e "/^\[p2p\]/,/^\[/{s/^[[:space:]]*persistent_peers *=.*/persistent_peers = \"$PEERS\"/}" $HOME/.fiamma/config/config.toml
```

#### create service file

```
sudo tee /etc/systemd/system/fiammad.service > /dev/null <<EOF
[Unit]
Description=Fiamma node
After=network-online.target
[Service]
User=$USER
WorkingDirectory=$HOME/.fiamma
ExecStart=$(which fiammad) start --home $HOME/.fiamma
Restart=on-failure
RestartSec=5
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF
```

#### enable and start service

```
sudo systemctl daemon-reload
sudo systemctl enable fiammad
sudo systemctl restart fiammad && sudo journalctl -u fiammad -f
```

#### Snapshot

#### Stop the service and reset the data <a href="#stop-the-service-and-reset-the-data" id="stop-the-service-and-reset-the-data"></a>

```
sudo systemctl stop fiammad
cp $HOME/.fiamma/data/priv_validator_state.json $HOME/.fiamma/priv_validator_state.json.backup
rm -rf $HOME/.fiamma/data $HOME/.fiamma/wasm
```

#### Download latest snapshot <a href="#download-latest-snapshot" id="download-latest-snapshot"></a>

```
curl https://snapshots.coinhunterstr.com/fiamma/snapshot_latest.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.fiamma
mv $HOME/.fiamma/priv_validator_state.json.backup $HOME/.fiamma/data/priv_validator_state.json
```

#### Restart the service and check the log <a href="#restart-the-service-and-check-the-log" id="restart-the-service-and-check-the-log"></a>

```
sudo systemctl restart fiammad && sudo journalctl -u fiammad -f
```

### Automatic Installation <a href="#auto-installation" id="auto-installation"></a>

> Moniker yerine Validator isminizi yazıp enter basın.

```
bash <(wget -qO- https://raw.githubusercontent.com/CoinHuntersTR/props/main/AutoInstall/fiamma.sh)
```

### Sync Node

> Node ağ ile eşleşmiş olması gerekiyor. Bunun için `fiammad status 2>&1 | jq` komutunu çalıştırdığınızda `false` çıktısı vermesi gerekir. `True` çıktı alırsanız aşağıdaki adımlara devam etmeyin.

### Run a Validator

```
cd $HOME
```

> İlk önce Pubkeyimizi alıyoruz.

```
fiammad comet show-validator
```

`{"@type":"/cosmos.crypto.ed25519.PubKey","key":"0LuMdRNJpWGiH+b+................"}` buna benzer bir çıktı alacaksınız.

> Sonrasında validator json dosyası açıyoruz.

> Aşağıdaki dosyayı kendinize göre düzenlemeyi unutmayın. Validator ismi, site linkleri vs.

```
cat << EOF > ~/.fiamma/config/validator.json
{
	"pubkey": {"@type":"/cosmos.crypto.ed25519.PubKey","key":"m0nifXztPm9lMpTSUNz6HaUXK26oJLRAdVqhUZJY/QU="},
	"amount": "20000ufia",
	"moniker": "Monikerİsmi",
	"identity": "",
	"website": "",
	"security": "",
	"details": "👑Coin Hunters Community",
	"commission-rate": "0.1",
	"commission-max-rate": "0.2",
	"commission-max-change-rate": "0.01",
	"min-self-delegation": "1"
}
EOF
```

> terminale yapıştırdıktan sonra, CTRL X Y enter ile çıkıyoruz.
>
> Şimdi tekrardan node restart atalım

```
sudo systemctl restart fiammad
```

> Şimdi aşağıdaki komutu çalıştırıyoruz. `wallet` yerine kendi cüzdan isminizi yazmayı unutmayın. Terminale cüzdan kurmak için `Useful Commands` bölümüne bakabilirsiniz.

```
fiammad tx staking create-validator ~/.fiamma/config/validator.json --from wallet --chain-id fiamma-testnet-1 --fees 2ufia
```
