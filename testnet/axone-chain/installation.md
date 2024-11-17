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
ver="1.22.8" 
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
rm -rf axoned
git clone https://github.com/axone-protocol/axoned.git
cd axoned
git checkout v10.0.0
```

#### Set Vars

> `Moniker` yerine validator adınızı ekliyoruz.

```
fiammad init $MONIKER --chain-id fiamma-testnet-1
sed -i -e "s|^node *=.*|node = \"tcp://localhost:26657\"|" $HOME/.axoned/config/client.toml
sed -i -e "s|^keyring-backend *=.*|keyring-backend = \"test\"|" $HOME/.axoned/config/client.toml
sed -i -e "s|^chain-id *=.*|chain-id = \"axone-dentrite-1\"|" $HOME/.axoned/config/client.toml
```

#### Download Genesis and Addrbook

```
wget -O $HOME/.fiamma/config/genesis.json  https://raw.githubusercontent.com/CoinHuntersTR/props/refs/heads/main/axone/genesis.json
wget -O $HOME/.fiamma/config/addrbook.json  https://raw.githubusercontent.com/CoinHuntersTR/props/refs/heads/main/axone/addrbook.json
```

#### Config Pruning

```
sed -i -e "s/^pruning *=.*/pruning = \"custom\"/" $HOME/.axoned/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"100\"/" $HOME/.axoned/config/app.toml
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"0\"/" $HOME/.axoned/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"19\"/" $HOME/.axoned/config/app.toml
sed -i 's|minimum-gas-prices =.*|minimum-gas-prices = "0uaxone"|g' $HOME/.axoned/config/app.toml
```

#### Set seeds and peers

```
URL="https://axone-testnet-rpc.polkachu.com/net_info"
response=$(curl -s $URL)
PEERS=$(echo $response | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):" + (.node_info.listen_addr | capture("(?<ip>.+):(?<port>[0-9]+)$").port)' | paste -sd "," -)
echo "PEERS=\"$PEERS\""

# Update the persistent_peers in the config.toml file
sed -i -e "s|^seeds *=.*|seeds = \"$SEEDS\"|; s|^persistent_peers *=.*|persistent_peers = \"$PEERS\"|" $HOME/.axoned/config/config.toml
```

#### create service file

```
sudo tee /etc/systemd/system/axoned.service > /dev/null <<EOF
[Unit]
Description=axone node
After=network-online.target
[Service]
User=$USER
WorkingDirectory=$HOME/.axoned
ExecStart=$(which axoned) start --home $HOME/.axoned
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
sudo systemctl enable axoned
sudo systemctl restart axoned && sudo journalctl -u axoned -f
```

#### Snapshot

#### Stop the service and reset the data <a href="#stop-the-service-and-reset-the-data" id="stop-the-service-and-reset-the-data"></a>

```
sudo systemctl stop axoned
cp $HOME/.axoned/data/priv_validator_state.json $HOME/.axoned/priv_validator_state.json.backup
rm -rf $HOME/.axoned/data
```

#### Download latest snapshot <a href="#download-latest-snapshot" id="download-latest-snapshot"></a>

```
curl https://snapshots.coinhunterstr.com/testnet/axone/snapshot_latest.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.axoned
mv $HOME/.axoned/priv_validator_state.json.backup $HOME/.axoned/data/priv_validator_state.json
```

#### Restart the service and check the log <a href="#restart-the-service-and-check-the-log" id="restart-the-service-and-check-the-log"></a>

```
sudo systemctl restart axoned && sudo journalctl -u axoned -f
```

### Automatic Installation <a href="#auto-installation" id="auto-installation"></a>

> Moniker yerine Validator isminizi yazıp enter basın.

```
bash <(wget -qO- https://raw.githubusercontent.com/CoinHuntersTR/props/refs/heads/main/AutoInstall/axone.sh)
```

### Sync Node

> Node ağ ile eşleşmiş olması gerekiyor. Bunun için `axoned status 2>&1 | jq` komutunu çalıştırdığınızda `false` çıktısı vermesi gerekir. `True` çıktı alırsanız aşağıdaki adımlara devam etmeyin.

### Run a Validator

```
cd $HOME
```

> İlk önce Pubkeyimizi alıyoruz.

```
axoned tendermint show-validator
```

`{"@type":"/cosmos.crypto.ed25519.PubKey","key":"0LuMdRNJpWGiH+b+................"}` buna benzer bir çıktı alacaksınız.

> Sonrasında validator json dosyası açıyoruz.

> Aşağıdaki dosyayı kendinize göre düzenlemeyi unutmayın. Validator ismi, site linkleri vs.

```
cat << EOF > ~/.axoned/config/validator.json
{
	"pubkey": {"@type":"/cosmos.crypto.ed25519.PubKey","key":"m0nifXztPm9lMpTSUNz6HaUXK26oJLRAdVqhUZJY/QU="},
	"amount": "1000000uaxone",
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
sudo systemctl restart axoned
```

> Şimdi aşağıdaki komutu çalıştırıyoruz. `wallet` yerine kendi cüzdan isminizi yazmayı unutmayın. Terminale cüzdan kurmak için `Useful Commands` bölümüne bakabilirsiniz.

```
axoned tx staking create-validator ~/.axoned/config/validator.json --from wallet --chain-id axone-dentrite-1 --fees 0uaxone -y
```
