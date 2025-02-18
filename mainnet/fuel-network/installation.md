# Installation

### Manual Installation <a href="#installation" id="installation"></a>

#### Gerekli Sistem <a href="#install-dependencies" id="install-dependencies"></a>

Ubuntu 22.04

<table><thead><tr><th width="279">CPU</th><th>RAM</th><th>SSD</th></tr></thead><tbody><tr><td>8 Cores</td><td>16GB RAM</td><td>500 SSD</td></tr></tbody></table>

#### Install dependencies <a href="#install-dependencies" id="install-dependencies"></a>

**UPDATE SYSTEM AND INSTALL BUILD TOOLS**

```
sudo apt update && sudo apt upgrade -y
sudo apt install curl git wget htop tmux build-essential jq make lz4 gcc unzip -y
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
wget -O fuelsequencerd https://github.com/FuelLabs/fuel-sequencer-deployments/releases/download/seq-mainnet-1.2-improved-sidecar/fuelsequencerd-seq-mainnet-1.2-improved-sidecar-linux-amd64
chmod +x fuelsequencerd
mv ~/fuelsequencerd ~/go/bin/
```

#### Set Vars

> `Moniker` yerine validator adınızı ekliyoruz.

```
fuelsequencerd init $MONIKER --chain-id seq-mainnet-1
sed -i \
-e "s/chain-id = .*/chain-id = \"seq-mainnet-1\"/" \
-e "s/keyring-backend = .*/keyring-backend = \"os\"/" \
-e "s/node = .*/node = \"tcp:\/\/localhost:26657\"/" $HOME/.fuelsequencer/config/client.toml
```

#### Download Genesis and Addrbook

```
wget -O $HOME/.fuelsequencer/config/genesis.json https://raw.githubusercontent.com/CoinHuntersTR/props/refs/heads/main/fuel/genesis.json
wget -O $HOME/.fuelsequencer/config/addrbook.json  https://raw.githubusercontent.com/CoinHuntersTR/props/refs/heads/main/fuel/addrbook.json
```

#### Config Pruning

```
sed -i -e "s/^pruning *=.*/pruning = \"custom\"/" $HOME/.fuelsequencer/config/app.toml 
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"100\"/" $HOME/.fuelsequencer/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"19\"/" $HOME/.fuelsequencer/config/app.toml

sed -i 's|minimum-gas-prices =.*|minimum-gas-prices = "10fuel"|g' $HOME/.fuelsequencer/config/app.toml
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.fuelsequencer/config/config.toml
sed -i -e "s/^indexer *=.*/indexer = \"null\"/" $HOME/.fuelsequencer/config/config.toml
```

#### Set seeds and peers

```
SEEDS="dc7b01b0379f660fb59223b9862cef0db11f14d9@152.53.121.42:19656,7e4d77ee264919f0e6dc4fde226278020418ea46@fuel-mainnet-seed.itrocket.net:63656"
PEERS="b3052ca64950786499d56ade68593a555e383ad4@fuel-mainnet-peer.itrocket.net:63656,d48507eb9c8fc6cab278da8b64548496134562dc@141.95.11.200:26656,9584099276b4baf2d6fdf07d4eb9dec40564bba5@185.107.68.171:26656,a419a5e73a3ac2e8ed81841b7a0f7ba6fb2cf78a@82.223.5.79:26656,bbb5f1939278b75efbc213067cc8226591353fc4@65.108.133.32:26656,cb6ae22e1e89d029c55f2cb400b0caa19cbe5523@38.132.56.24:32680,7198cf1a7a7da216444bf9e6fc5b43fd123e8a0a@57.129.38.180:58456,5d75cca90b178f2b782ce57b0067c0ec8512354c@65.109.70.89:41656,0d7efe1a993e548acccba23358de50a87f5ac841@176.103.222.150:26656,9eb2801d1dde4b9ff0be3427092cc2548e973d71@176.103.222.162:26656,40369a07f904d01262141353b5b8fcc8fae2b9da@65.109.53.189:26656"
sed -i -e "/^\[p2p\]/,/^\[/{s/^[[:space:]]*seeds *=.*/seeds = \"$SEEDS\"/}" \
       -e "/^\[p2p\]/,/^\[/{s/^[[:space:]]*persistent_peers *=.*/persistent_peers = \"$PEERS\"/}" \
       $HOME/.fuelsequencer/config/config.toml
```

#### create service file

```
sudo tee /etc/systemd/system/fuelsequencerd.service > /dev/null <<EOF
[Unit]
Description=Fuel node
After=network-online.target
[Service]
User=$USER
WorkingDirectory=$HOME/.fuelsequencer
ExecStart=$(which fuelsequencerd) start --home $HOME/.fuelsequencer
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
sudo systemctl enable fuelsequencerd
sudo systemctl restart fuelsequencerd && sudo journalctl -u fuelsequencerd -fo cat
```

### Automatic Installation <a href="#auto-installation" id="auto-installation"></a>

> Moniker yerine Validator isminizi yazıp enter basın.

```
bash <(wget -qO- https://raw.githubusercontent.com/CoinHuntersTR/props/main/AutoInstall/fuel.sh)
```

### Sync Node

> Node ağ ile eşleşmiş olması gerekiyor. Bunun için `fuelsequencerd status 2>&1 | jq` komutunu çalıştırdığınızda `false` çıktısı vermesi gerekir. `True` çıktı alırsanız aşağıdaki adımlara devam etmeyin.

### Run a Validator

```
cd $HOME
```

> İlk önce Pubkeyimizi alıyoruz.

```
fuelsequencerd comet show-validator
```

`{"@type":"/cosmos.crypto.ed25519.PubKey","key":"0LuMdRNJpWGiH+b+................"}` buna benzer bir çıktı alacaksınız.

> Sonrasında validator json dosyası açıyoruz.

> Aşağıdaki dosyayı kendinize göre düzenlemeyi unutmayın. Validator ismi, site linkleri vs.

```
cat << EOF > ~/.fuelsequencer/config/validator.json
{
	"pubkey": {"@type":"/cosmos.crypto.ed25519.PubKey","key":"BCDw/cq+7VxwNU/UIDc08JaYXru0Wa8SPoamzYSHfY8="},
	"amount": "1000000000fuel",
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
sudo systemctl restart fuelsequencerd && sudo journalctl -u fuelsequencerd -fo cat
```

> Şimdi aşağıdaki komutu çalıştırıyoruz. `wallet` yerine kendi cüzdan isminizi yazmayı unutmayın. Terminale cüzdan kurmak için `Useful Commands` bölümüne bakabilirsiniz.

```
fuelsequencerd tx staking create-validator /root/.fuelsequencer/config/validator.json --from wallet --chain-id seq-mainnet-1 --fees 3000000fuel -y
```
