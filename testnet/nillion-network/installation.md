# Installation

### Manual Installation <a href="#installation" id="installation"></a>

#### Gerekli Sistem <a href="#install-dependencies" id="install-dependencies"></a>

Ubuntu 22.04

<table><thead><tr><th width="279">CPU</th><th>RAM</th><th>SSD</th></tr></thead><tbody><tr><td>4 vCPU</td><td>8 GB RAM</td><td>160 SSD</td></tr></tbody></table>

#### Install dependencies <a href="#install-dependencies" id="install-dependencies"></a>

**UPDATE SYSTEM AND INSTALL BUILD TOOLS**

```
sudo apt -q update
sudo apt -qy install curl git jq lz4 build-essential
sudo apt -qy upgrade
```

**INSTALL GO**

```
sudo rm -rf /usr/local/go
curl -Ls https://go.dev/dl/go1.22.4.linux-amd64.tar.gz | sudo tar -xzf - -C /usr/local
eval $(echo 'export PATH=$PATH:/usr/local/go/bin' | sudo tee /etc/profile.d/golang.sh)
eval $(echo 'export PATH=$PATH:$HOME/go/bin' | tee -a $HOME/.profile)
```

#### Download and build binaries <a href="#download-and-build-binaries" id="download-and-build-binaries"></a>

```bash
mkdir -p $HOME/.nillionapp/cosmovisor/genesis/bin
wget -O $HOME/.nillionapp/cosmovisor/genesis/bin/nilchaind https://snapshots.kjnodes.com/nillion-testnet/nilchaind-v0.2.2-linux-amd64
chmod +x $HOME/.nillionapp/cosmovisor/genesis/bin/nilchaind

# Create application symlinks

sudo ln -s $HOME/.nillionapp/cosmovisor/genesis $HOME/.nillionapp/cosmovisor/current -f
sudo ln -s $HOME/.nillionapp/cosmovisor/current/bin/nilchaind /usr/local/bin/nilchaind -f

# Upgrades
mkdir -p $HOME/.nillionapp/cosmovisor/upgrades/v0.2.1/bin
wget -O $HOME/.nillionapp/cosmovisor/upgrades/v0.2.1/bin/nilchaind https://snapshots.kjnodes.com/nillion-testnet/nilchaind-v0.2.1-linux-amd64
chmod +x $HOME/.nillionapp/cosmovisor/upgradesv0.2.1/bin/nilchaind
```

#### create service file

```bash
# Download and install Cosmovisor
go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@v1.5.0

# Create service
sudo tee /etc/systemd/system/nillion.service > /dev/null << EOF
[Unit]
Description=nillion node service
After=network-online.target

[Service]
User=root
ExecStart=/root/go/bin/cosmovisor run start --home=/root/.nillionapp
Restart=on-failure
RestartSec=10
LimitNOFILE=65535
Environment="DAEMON_HOME=/root/.nillionapp"
Environment="DAEMON_NAME=nilchaind"
Environment="UNSAFE_SKIP_BACKUP=true"

[Install]
WantedBy=multi-user.target
EOF
```

#### Set Node

```bash
nilchaind config set client chain-id nillion-chain-testnet-1
nilchaind config set client keyring-backend test
nilchaind config set client node tcp://localhost:18057
```

> `Moniker` yerine validator adınızı ekliyoruz.

```bash
nilchaind init Moniker --chain-id nillion-chain-testnet-1 --home=$HOME/.nillionapp
```

#### Download Genesis and Addrbook

```bash
curl -Ls https://raw.githubusercontent.com/CoinHuntersTR/props/main/nillion/genesis.json > $HOME/.nillionapp/config/genesis.json
curl -Ls https://raw.githubusercontent.com/CoinHuntersTR/props/main/nillion/addrbook.json > $HOME/.nillionapp/config/addrbook.json

# Add seeds
sed -i -e "s|^seeds *=.*|seeds = \"3f472746f46493309650e5a033076689996c8881@nillion-testnet.rpc.kjnodes.com:18059\"|" $HOME/.nillionapp/config/config.toml

# Set minimum gas price
sed -i -e "s|^minimum-gas-prices *=.*|minimum-gas-prices = \"0unil\"|" $HOME/.nillionapp/config/app.toml
```

#### Config Pruning

```
sed -i \
  -e 's|^pruning *=.*|pruning = "custom"|' \
  -e 's|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|' \
  -e 's|^pruning-keep-every *=.*|pruning-keep-every = "0"|' \
  -e 's|^pruning-interval *=.*|pruning-interval = "19"|' \
  $HOME/.nillionapp/config/app.toml
```

#### Set seeds and peers

```
PEERS=$(curl -sS https://junction-rpc.dymion.cloud/net_info | \
jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | \
awk -F ':' '{printf "%s:%s%s", $1, $(NF), NR==NF?"":","}')
echo "$PEERS"

sed -i 's|^persistent_peers *=.*|persistent_peers = "'$PEERS'"|' $HOME/.junction/config/config.toml
```

#### Custom Port

```bash
# Set custom ports
sed -i -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:18058\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:18057\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:18060\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:18056\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":18066\"%" $HOME/.nillionapp/config/config.toml
sed -i -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:18017\"%; s%^address = \":8080\"%address = \":18080\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:18090\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:18091\"%; s%:8545%:18045%; s%:8546%:18046%; s%:6065%:18065%" $HOME/.nillionapp/config/app.toml
```

#### Snapshot

```bash
curl -L https://snapshots.coinhunterstr.com/nillion/snapshot_latest.tar.lz4 | tar -Ilz4 -xf - -C $HOME/.nillionapp
[[ -f $HOME/.nillionapp/data/upgrade-info.json ]] && cp $HOME/.nillionapp/data/upgrade-info.json $HOME/.nillionapp/cosmovisor/genesis/upgrade-info.json
```

#### Restart the service and check the log <a href="#restart-the-service-and-check-the-log" id="restart-the-service-and-check-the-log"></a>

```
sudo systemctl start nillion.service && sudo journalctl -u nillion.service -f --no-hostname -o cat
```

### Automatic Installation <a href="#auto-installation" id="auto-installation"></a>

> cüzdan adınızı ve Validator adınızı girip, istediğiniz bir port numarası ekleyerek otomatik kurulum yapabilirsiniz. (hiçbir değişiklik istemiyorsanız port nuumarası olarak 26 yazabilirsiniz.)

```
bash <(wget -qO- https://raw.githubusercontent.com/CoinHuntersTR/props/main/AutoInstall/nibiru.sh)
```

### Sync Node

> Node ağ ile eşleşmiş olması gerekiyor. Bunun için `nilchaind status 2>&1 | jq` komutunu çalıştırdığınızda `false` çıktısı vermesi gerekir. `True` çıktı alırsanız aşağıdaki adımlara devam etmeyin.

### Run a Validator

> İlk önce Pubkeyimizi alıyoruz.

```
nilchaind comet show-validator
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
	"pubkey": bir üstte çıkan pubkey buraya ekliyoruz.,
	"amount": "100000unil",
	"moniker": "Validator isminizi yazıyoruz.",
	"identity": "keybase sitesinden aldığımız ID yazıyoruz.",
	"website": "twitter yada github gibi yerleri verebilirsiniz. ",
	"security": "mail adresiniz.",
	"details": "istediğiniz bir şey yazabilir yada boş bırakabilirsiniz.",
	"commission-rate": "0.1",
	"commission-max-rate": "0.2",
	"commission-max-change-rate": "0.01",
	"min-self-delegation": "1"
}
```

> &#x20;Gerekli yerleri doldurduktan sonra, terminale yapıştırdıktan sonra, CTRL X Y enter ile çıkıyoruz.
>
> Şimdi tekrardan node restart atalım

```bash
sudo systemctl restart nillion.service
```

> Şimdi aşağıdaki komutu çalıştırıyoruz. `wallet` yerine kendi cüzdan isminizi yazmayı unutmayın. Terminale cüzdan kurmak için `Useful Commands` bölümüne bakabilirsiniz.

```
nilchaind tx staking create-validator $HOME/validator.json --from wallet --chain-id nillion-chain-testnet-1 --fees 0unil
```
