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
```

**INSTALL GO**

```
ver="1.21.6" 
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
wget -O junctiond https://github.com/airchains-network/junction/releases/download/v0.3.1/junctiond-linux-amd64
chmod +x junctiond
mv junctiond $HOME/go/bin/
```

#### Set Vars

> `Moniker` yerine validator adınızı ekliyoruz.

```
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y
source $HOME/.cargo/env
junctiond init $MONIKER --chain-id varanasi-1
```

#### Config init app

```
sed -i -e "s|^node *=.*|node = \"tcp://localhost:26657\"|" $HOME/.junctiond/config/client.toml
```

#### Download Genesis and Addrbook

```
wget -O $HOME/.junctiond/config/genesis.json https://server-7.itrocket.net/testnet/airchains_v/genesis.json
wget -O $HOME/.junctiond/config/addrbook.json  https://server-7.itrocket.net/testnet/airchains_v/addrbook.json
```

#### Config Pruning

```
# config pruning
sed -i -e "s/^pruning *=.*/pruning = \"custom\"/" $HOME/.junctiond/config/app.toml 
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"100\"/" $HOME/.junctiond/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"19\"/" $HOME/.junctiond/config/app.toml

# set minimum gas price, enable prometheus and disable indexing
sed -i 's|minimum-gas-prices =.*|minimum-gas-prices = "0.001uamf"|g' $HOME/.junctiond/config/app.toml
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.junctiond/config/config.toml
sed -i -e "s/^indexer *=.*/indexer = \"null\"/" $HOME/.junctiond/config/config.toml
```

#### Set seeds and peers

```
SEEDS="97cadd453fa35cee05f72611fdb15a49112575cb@airchains_v-testnet-seed.itrocket.net:19656"
PEERS="79f26210777e84efb600bf776c32615a72675d9f@airchains_v-testnet-peer.itrocket.net:19656,859485b13c2d8ab3888ffc11d1c506d78f681317@5.9.116.21:26756,8c229309660496e71b8a9d1edee46a18693b8e70@65.109.111.234:19656,db686fcfdf0b4676d601d5beb11faee5ad96bff1@37.27.71.199:28656,0b4e78189c9148dda5b1b98c6e46b764337558a3@91.227.33.18:19656,4eff6ecc2323811d18c7e06319b2d8bbf58590d1@65.108.233.73:19656,b43f7c96bb780d9ac535d3c1f78092cf8c455e85@104.36.23.246:26656,b107bf75ca12c4f5fa544390e27f8104b13c7f1b@[2001:41d0:1004:1596::1]:13756,3650f3737940af2d6cc8d17244706505648ff639@212.56.32.148:14156,847ffe6f885e4dd3ea97e5d558ee1bca1cc3fe9d@213.136.91.3:19656,43c265128fd9be02721df03e8ba4bcf8c982a062@1.53.252.54:26656"
sed -i -e "/^\[p2p\]/,/^\[/{s/^[[:space:]]*seeds *=.*/seeds = \"$SEEDS\"/}" \
       -e "/^\[p2p\]/,/^\[/{s/^[[:space:]]*persistent_peers *=.*/persistent_peers = \"$PEERS\"/}" $HOME/.junctiond/config/config.toml
```

#### create service file

```
sudo tee /etc/systemd/system/junctiond.service > /dev/null <<EOF
[Unit]
Description=Airchains_v node
After=network-online.target
[Service]
User=$USER
WorkingDirectory=$HOME/.junctiond
ExecStart=$(which junctiond) start --home $HOME/.junctiond
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
sudo systemctl enable junctiond
sudo systemctl restart junctiond && sudo journalctl -u junctiond -fo cat
```

#### Snapshot

#### Stop the service and reset the data <a href="#stop-the-service-and-reset-the-data" id="stop-the-service-and-reset-the-data"></a>

```
sudo systemctl stop junctiond
cp $HOME/.junctiond/data/priv_validator_state.json $HOME/.junction/priv_validator_state.json.backup
rm -rf $HOME/.junctiond/data
```

#### Download latest snapshot <a href="#download-latest-snapshot" id="download-latest-snapshot"></a>

```
curl -L https://snapshots.coinhunterstr.com/data.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.junctiond
mv $HOME/.junctiond/priv_validator_state.json.backup $HOME/.junctiond/data/priv_validator_state.json
```

#### Restart the service and check the log <a href="#restart-the-service-and-check-the-log" id="restart-the-service-and-check-the-log"></a>

```
sudo systemctl start junctiond && sudo journalctl -u junctiond -f --no-hostname -o cat
```

### Automatic Installation <a href="#auto-installation" id="auto-installation"></a>

> Moniker yerine Validator isminizi yazıp enter basın.

```
wget -q -O Airchains.sh https://raw.githubusercontent.com/CoinHuntersTR/props/main/AutoInstall/Airchains.sh && chmod +x Airchains.sh && ./Airchains.sh
```

### Sync Node

> Node ağ ile eşleşmiş olması gerekiyor. Bunun için `junctiond status 2>&1 | jq` komutunu çalıştırdığınızda `false` çıktısı vermesi gerekir. `True` çıktı alırsanız aşağıdaki adımlara devam etmeyin.

### Run a Validator

> İlk önce Pubkeyimizi alıyoruz.

```
junctiond comet show-validator
```

`{"@type":"/cosmos.crypto.ed25519.PubKey","key":"0LuMdRNJpWGiH+b+................"}` buna benzer bir çıktı alacaksınız.

```
cd $HOME
```

> Sonrasında validator json dosyası açıyoruz.

```
nano /root/validator.json
```

> Aşağıdaki dosyayı kendinize göre düzenlemeyi unutmayın. Validator ismi, site linkleri vs.

```
{
	"pubkey": <validator-pub-key>,
	"amount": "9900000amf",
	"moniker": "<validator-name>",
	"identity": "optional identity signature (ex. UPort or Keybase)",
	"website": "validator's (optional) website",
	"security": "validator's (optional) security contact email",
	"details": "validator's (optional) details",
	"commission-rate": "0.1",
	"commission-max-rate": "0.2",
	"commission-max-change-rate": "0.01",
	"min-self-delegation": "1"
}
```

> terminale yapıştırdıktan sonra, CTRL X Y enter ile çıkıyoruz.
>
> Şimdi tekrardan node restart atalım

```
sudo systemctl restart junctiond
```

> Şimdi aşağıdaki komutu çalıştırıyoruz. `wallet` yerine kendi cüzdan isminizi yazmayı unutmayın. Terminale cüzdan kurmak için `Useful Commands` bölümüne bakabilirsiniz.

```
junctiond tx staking create-validator $HOME/validator.json --from wallet --chain-id junction --fees 5000amf
```
