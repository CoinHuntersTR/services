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
wget -O sunrised wget https://github.com/sunriselayer/sunrise/releases/download/v0.2.0/sunrised
chmod +x $HOME/sunrised
mv $HOME/sunrised $HOME/go/bin/sunrised
```

#### Set Vars

> `Moniker` yerine validator adınızı ekliyoruz.

```
sunrised init $MONIKER --chain-id sunrise-test-0.2
sed -i -e "s|^node *=.*|node = \"tcp://localhost:26657\"|" $HOME/.sunrise/config/client.toml
sed -i -e "s|^keyring-backend *=.*|keyring-backend = \"os\"|" $HOME/.sunrise/config/client.toml
sed -i -e "s|^chain-id *=.*|chain-id = \"sunrise-test-0.2\"|" $HOME/.sunrise/config/client.toml
```

#### Download Genesis and Addrbook

```
wget -O $HOME/.sunrise/config/genesis.json https://raw.githubusercontent.com/CoinHuntersTR/props/main/sunrise/genesis.json
```

#### Config Pruning

```
sed -i -e "s/^pruning *=.*/pruning = \"custom\"/" $HOME/.sunrise/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"100\"/" $HOME/.sunrise/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"50\"/" $HOME/.sunrise/config/app.toml
sed -i 's|minimum-gas-prices =.*|minimum-gas-prices = "0.002urise"|g' $HOME/.sunrise/config/app.toml
```

#### Set seeds and peers

```
SEEDS="0c0e0cf617c1c58297f53f3a82cea86a7c860396@a.sunrise-test-1.cauchye.net:26656,db223ecc4fba0e7135ba782c0fd710580c5213a6@a-node.sunrise-test-1.cauchye.net:26656,82bc2fdbfc735b1406b9da4181036ab9c44b63be@b-node.sunrise-test-1.cauchye.net:26656"
PEERS="edf6ccc9678e4614295cb4479c2dae1b83e06500@37.27.31.124:26656"
sed -i -e "/^\[p2p\]/,/^\[/{s/^[[:space:]]*seeds *=.*/seeds = \"$SEEDS\"/}" \
       -e "/^\[p2p\]/,/^\[/{s/^[[:space:]]*persistent_peers *=.*/persistent_peers = \"$PEERS\"/}" $HOME/.fiamma/config/config.toml
```

#### create service file

```
sudo tee /etc/systemd/system/sunrised.service > /dev/null <<EOF
[Unit]
Description=sunrise node
After=network-online.target
[Service]
User=$USER
WorkingDirectory=$HOME/.sunrise
ExecStart=$(which sunrised) start --home $HOME/.sunrise
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
sudo systemctl enable sunrised
sudo systemctl restart sunrised && sudo journalctl -u sunrised -f
```

### Automatic Installation <a href="#auto-installation" id="auto-installation"></a>

> Moniker yerine Validator isminizi yazıp enter basın.

```
bash <(wget -qO- https://raw.githubusercontent.com/CoinHuntersTR/props/main/AutoInstall/sunrise.sh)
```

### Sync Node

> Node ağ ile eşleşmiş olması gerekiyor. Bunun için `sunrised status 2>&1 | jq` komutunu çalıştırdığınızda `false` çıktısı vermesi gerekir. `True` çıktı alırsanız aşağıdaki adımlara devam etmeyin.

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
cat << EOF > ~/.sunrise/config/validator.json
{
	"pubkey": {"@type":"/cosmos.crypto.ed25519.PubKey","key":"m0nifXztPm9lMpTSUNz6HaUXK26oJLRAdVqhUZJY/QU="},
	"amount": "9000000000000uvrise",
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
sudo systemctl restart sunrised && sudo journalctl -u sunrised -f
```

> Şimdi aşağıdaki komutu çalıştırıyoruz. `wallet` yerine kendi cüzdan isminizi yazmayı unutmayın. Terminale cüzdan kurmak için `Useful Commands` bölümüne bakabilirsiniz.

```
sunrised tx staking create-validator ~/.sunrise/config/validator.json --from wallet --chain-id sunrise-test-0.1 --fees 0.0uvrise
```
