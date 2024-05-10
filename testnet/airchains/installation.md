# Installation

### Manual Installation <a href="#installation" id="installation"></a>

#### Gerekli Sistem <a href="#install-dependencies" id="install-dependencies"></a>

Ubuntu 22.04

<table><thead><tr><th width="279">CPU</th><th>RAM</th><th>SSD</th></tr></thead><tbody><tr><td>4 vCPU</td><td>8 GB RAM</td><td>160 SSD</td></tr></tbody></table>

#### Install dependencies <a href="#install-dependencies" id="install-dependencies"></a>

**UPDATE SYSTEM AND INSTALL BUILD TOOLS**

```
sudo apt update && sudo apt upgrade -y
sudo apt install curl tar wget clang pkg-config libssl-dev jq build-essential git make lz4 unzip ncdu -y
```

**INSTALL GO**

```
ver="1.21.5" 
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
wget https://github.com/airchains-network/junction/releases/download/v0.1.0/junctiond
chmod +x junctiond
sudo mv junctiond /usr/local/bin
```

#### Set Vars

> `Moniker` yerine validator adınızı ekliyoruz.

```
junctiond config chain-id junction
junctiond init "Moniker" --chain-id junction
```

#### Config init app

```
wardend init $MONIKER
sed -i -e "s|^node *=.*|node = \"tcp://localhost:${WARDEN_PORT}657\"|" $HOME/.warden/config/client.toml
```

#### Download Genesis and Addrbook

```
sudo wget -O $HOME/.junction/config/genesis.json https://raw.githubusercontent.com/CoinHuntersTR/props/main/Airchains/genesis.json
sudo wget -O $HOME/.junction/config/addrbook.json https://raw.githubusercontent.com/CoinHuntersTR/props/main/Airchains/addrbook.json

sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"5000amf\"/;" ~/.junction/config/app.toml
```

#### Config Pruning

```
pruning="custom" && \
pruning_keep_recent="100" && \
pruning_keep_every="0" && \
pruning_interval="10" && \
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.junction/config/app.toml && \
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.junction/config/app.toml && \
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.junction/config/app.toml && \
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.junction/config/app.toml
```

#### Set seeds and peers

```
PEERS=$(curl -sS https://junction-rpc.dymion.cloud/net_info | \
jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | \
awk -F ':' '{printf "%s:%s%s", $1, $(NF), NR==NF?"":","}')
echo "$PEERS"

sed -i 's|^persistent_peers *=.*|persistent_peers = "'$PEERS'"|' $HOME/.junction/config/config.toml
```

#### Snapshot

```
junctiond tendermint unsafe-reset-all --home ~/.junction/ --keep-addr-book
curl https://files.dymion.cloud/junction/data.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.junction
```

#### create service file

```
sudo tee /etc/systemd/system/junctiond.service > /dev/null <<EOF
[Unit]
Description=junction
After=network-online.target
[Service]
User=$USER
ExecStart=$(which junctiond) start
Restart=always
RestartSec=3
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF
sudo systemctl daemon-reload
sudo systemctl enable junctiond
```

#### enable and start service

```
sudo systemctl daemon-reload
sudo systemctl enable junctiond
sudo systemctl restart junctiond && sudo journalctl -u junctiond -f
```

### Automatic Installation <a href="#auto-installation" id="auto-installation"></a>

> Moniker yerine Validator isminizi yazıp enter basın.

```
wget -q -O Airchains.sh https://raw.githubusercontent.com/CoinHuntersTR/props/main/AutoInstall/Airchains.sh && chmod +x Airchains.sh && ./Airchains.sh
```

### Sync Node

> Node ağ ile eşleşmiş olması gerekiyor. Bunun için `junctiond status 2>&1 | jq` komutunu çalıştırdığınızda `false` çıktısı vermesi gerekir.  `True` çıktı alırsanız aşağıdaki adımlara devam etmeyin.&#x20;

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

> terminale yapıştırdıktan sonra, CTRL X Y enter ile çıkıyoruz.&#x20;
>
> Şimdi tekrardan node restart atalım

```
sudo systemctl restart junctiond
```

> Şimdi aşağıdaki komutu çalıştırıyoruz. `wallet` yerine kendi cüzdan isminizi yazmayı unutmayın. Terminale cüzdan kurmak için `Useful Commands` bölümüne bakabilirsiniz.&#x20;

```
junctiond tx staking create-validator $HOME/validator.json --from wallet--chain-id junction --fees 5000amf
```

