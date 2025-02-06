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
rm -rf side
git clone https://github.com/sideprotocol/side.git
cd side
git checkout v1.0.0
make install
```

#### Set Vars

> `Moniker` yerine validator adınızı ekliyoruz.

```
sided config node tcp://localhost:26657
sided config keyring-backend os
sided config chain-id sidechain-1
sided init "test" --chain-id sidechain-1
```

#### Download Genesis and Addrbook

```
wget -O $HOME/.side/config/genesis.json https://server-2.itrocket.net/mainnet/side/genesis.json
wget -O $HOME/.side/config/addrbook.json  https://server-2.itrocket.net/mainnet/side/addrbook.json
```

#### Config Pruning

```
sed -i -e "s/^pruning *=.*/pruning = \"custom\"/" $HOME/.side/config/app.toml 
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"100\"/" $HOME/.side/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"19\"/" $HOME/.side/config/app.toml

sed -i 's|minimum-gas-prices =.*|minimum-gas-prices = "0.005uside"|g' $HOME/.side/config/app.toml
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.side/config/config.toml
sed -i -e "s/^indexer *=.*/indexer = \"null\"/" $HOME/.side/config/config.toml
```

#### Set seeds and peers

```
SEEDS="9355d3fe49475485444f64db4745dd8a970d7a72@side-mainnet-seed.itrocket.net:18656,a1c99cc234a524e53db8eb44e0c7df7115edd1b4@rpc.side.nodestake.org:666,ade4d8bc8cbe014af6ebdf3cb7b1e9ad36f412c0@seeds.polkachu.com:26356"
PEERS="cfc22ac13d20a6f3bd394a8a2dba787bc10c1b32@side-mainnet-peer.itrocket.net:14656,973538e4eb39bac08c9675830239a6358a1e442c@195.201.59.216:26656,8f5a8d7d6c29cd24bc2f844494c75d5044913b53@176.9.124.52:26356,db1df6aed42324c975209edceeba0daf6e8b0bab@160.202.131.55:24656,fc4192d1f80d783dec495abe4101169183d94190@8.52.153.92:14656,4192e340dc7a5e297143e271daf6b52e9e6aea0d@195.14.6.192:26656,24224badba137eb775916d9d5c4ff8f3ceff874b@[2a03:cfc0:8000:13::b910:27be]:11056,05cb5856192b389cff8c3851e0d30ae6a400187d@143.198.41.115:26656,75da8087bdc75ba0eed3c20a0c7a055721ecdb00@46.232.248.39:18656,b34c1431376443769554d89a3737ad65015a16a7@91.134.9.162:26356"
sed -i -e "/^\[p2p\]/,/^\[/{s/^[[:space:]]*seeds *=.*/seeds = \"$SEEDS\"/}" \
       -e "/^\[p2p\]/,/^\[/{s/^[[:space:]]*persistent_peers *=.*/persistent_peers = \"$PEERS\"/}" $HOME/.side/config/config.toml
```

#### create service file

```
sudo tee /etc/systemd/system/sided.service > /dev/null <<EOF
[Unit]
Description=Side node
After=network-online.target
[Service]
User=$USER
WorkingDirectory=$HOME/.side
ExecStart=$(which sided) start --home $HOME/.side
Restart=on-failure
RestartSec=5
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF
```

#### enable and start service

<pre><code>sudo systemctl daemon-reload
sudo systemctl enable sided
<strong>sudo systemctl restart sided &#x26;&#x26; sudo journalctl -u sided -fo cat
</strong></code></pre>

### Automatic Installation <a href="#auto-installation" id="auto-installation"></a>

> Moniker yerine Validator isminizi yazıp enter basın.

```
bash <(wget -qO- https://raw.githubusercontent.com/CoinHuntersTR/props/main/AutoInstall/side.sh)
```

### Sync Node

> Node ağ ile eşleşmiş olması gerekiyor. Bunun için `sided status 2>&1 | jq` komutunu çalıştırdığınızda `false` çıktısı vermesi gerekir. `True` çıktı alırsanız aşağıdaki adımlara devam etmeyin.

### Run a Validator

```
cd $HOME
```

> İlk önce Pubkeyimizi alıyoruz.

```
sunrised comet show-validator
```

`{"@type":"/cosmos.crypto.ed25519.PubKey","key":"0LuMdRNJpWGiH+b+................"}` buna benzer bir çıktı alacaksınız.

> Sonrasında validator json dosyası açıyoruz.

> Aşağıdaki dosyayı kendinize göre düzenlemeyi unutmayın. Validator ismi, site linkleri vs.

```
cat << EOF > ~/.side/config/validator.json
{
	"pubkey": {"@type":"/cosmos.crypto.ed25519.PubKey","key":"BCDw/cq+7VxwNU/UIDc08JaYXru0Wa8SPoamzYSHfY8="},
	"amount": "1000000uside",
	"moniker": "",
	"identity": "",
	"website": "",
	"security": "",
	"details": "",
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
sudo systemctl restart sided && sudo journalctl -u sided -fo cat
```

> Şimdi aşağıdaki komutu çalıştırıyoruz. `wallet` yerine kendi cüzdan isminizi yazmayı unutmayın. Terminale cüzdan kurmak için `Useful Commands` bölümüne bakabilirsiniz.

```
sided tx staking create-validator ~/.side/config/validator.json --from wallet --chain-id sidechain-1 --fees 100000uside
```
