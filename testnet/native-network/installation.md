# Installation

## Manual Installation <a href="#installation" id="installation"></a>

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
ver="1.23.0" 
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
wget -O gonative-v0.1.1-linux-amd64.gz https://github.com/gonative-cc/gonative/releases/download/v0.1.1/gonative-v0.1.1-linux-amd64.gz
gunzip gonative-v0.1.1-linux-amd64.gz
mv gonative-v0.1.1-linux-amd64 gonative
chmod +x gonative
mv gonative $HOME/go/bin/
echo "export PATH=$PATH:$HOME/go/bin" >> ~/.bashrc
source ~/.bashrc
```

#### Set Vars

> `Moniker` yerine validator adınızı ekliyoruz.

```
gonative config set client keyring-backend os
gonative config set client chain-id native-t1
gonative init $MONIKER --chain-id native-t1
```

#### Download Genesis and Addrbook

```
wget -O $HOME/.gonative/config/genesis.json https://raw.githubusercontent.com/CoinHuntersTR/props/refs/heads/main/native/genesis.json
wget -O $HOME/.gonative/config/addrbook.json https://raw.githubusercontent.com/CoinHuntersTR/props/refs/heads/main/native/addrbook.json
```

### Update config.toml
```
cat > $HOME/.gonative/config/config.toml << EOF
minimum-gas-prices = "0.08untiv"
pruning = "custom"
pruning-keep-recent = "100"
pruning-keep-every = "0"
pruning-interval = "50"
halt-height = 0
halt-time = 0
min-retain-blocks = 0
inter-block-cache = true
index-events = []

[telemetry]
enabled = true
prometheus-retention-time = 60

[api]
enable = true
swagger = true
address = "tcp://0.0.0.0:1317"
max-open-connections = 1000

[grpc]
enable = false
address = "0.0.0.0:9090"

[grpc-web]
enable = false
address = "0.0.0.0:9091"

[state-sync]
snapshot-interval = 0
snapshot-keep-recent = 2

[p2p]
laddr = "tcp://0.0.0.0:26656"
external-address = "$(wget -qO- eth0.me):26656"
seeds = "${SEEDS}"
persistent-peers = "${PEERS}"
max-num-inbound-peers = 50
max-num-outbound-peers = 50
max-connections = 100
handshake-timeout = "20s"
dial-timeout = "3s"

[mempool]
size = 5000
max-tx-bytes = 1048576
max-batch-bytes = 0

[consensus]
wal-file = "data/cs.wal/wal"
timeout-propose = "2s"
timeout-propose-delta = "500ms"
timeout-prevote = "1s"
timeout-prevote-delta = "500ms"
timeout-precommit = "1s"
timeout-precommit-delta = "500ms"
timeout-commit = "3s"
skip-timeout-commit = false
create-empty-blocks = true
create-empty-blocks-interval = "0s"
peer-gossip-sleep-duration = "100ms"
peer-query-maj23-sleep-duration = "2s"
EOF

### Update app.toml
cat > $HOME/.gonative/config/app.toml << EOF
minimum-gas-prices = "0.08untiv"
pruning = "custom"
pruning-keep-recent = "100"
pruning-keep-every = "0"
pruning-interval = "50"
halt-height = 0
halt-time = 0

[telemetry]
enabled = true
prometheus-retention-time = 60

[api]
enable = true
swagger = true
address = "tcp://0.0.0.0:1317"
max-open-connections = 1000

[grpc]
enable = false
address = "0.0.0.0:9090"

[state-sync]
snapshot-interval = 0
snapshot-keep-recent = 2
EOF
```

### create service file
```
sudo tee /etc/systemd/system/gonatived.service > /dev/null <<EOF
[Unit]
Description=gonative node
After=network-online.target

[Service]
User=$USER
ExecStart=$(which gonative) start
Restart=always
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```
### enable and start service
```
sudo systemctl daemon-reload
sudo systemctl enable gonatived
sudo systemctl restart gonatived && sudo journalctl -fu gonatived -o cat
```
## Automatic Installation <a href="#auto-installation" id="auto-installation"></a>

> Moniker yerine Validator isminizi yazıp enter basın.

```
bash <(wget -qO- https://raw.githubusercontent.com/CoinHuntersTR/props/refs/heads/main/AutoInstall/native.sh)
```

### Sync Node

> Node ağ ile eşleşmiş olması gerekiyor. Bunun için `gonative status 2>&1 | jq` komutunu çalıştırdığınızda `false` çıktısı vermesi gerekir. `True` çıktı alırsanız aşağıdaki adımlara devam etmeyin.

## Run a Validator

```
cd $HOME
```

> Node sync olduktan sonra bir tane cüzdan oluşturuyoruz.
>
> ```
> # yeni cüzdan oluşturmak için wallet yerine istediğiniz bir ismi yazın.
> gonative keys add wallet
>
> # Var olan bir cüzdan eklemek için aşağıdaki komutu kullanabilirsiniz. 
> gonative keys add wallet--recover
> ```

> Aşağıdaki dosyayı kendinize göre düzenlemeyi unutmayın. Validator ismi, site linkleri vs.

```
gonative tx staking create-validator \
--amount=1000000untiv \
--pubkey=$(gonative comet show-validator) \
--moniker="<Your moniker>" \
--identity=<Your identity> \
--details="<Your details>" \
--chain-id=native-t1 \
--commission-rate=0.05 \
--commission-max-rate=0.20 \
--commission-max-change-rate=0.1 \
--min-self-delegation=1 \
--from=<YOUR_WALLET> \
--fees=20000untiv \
-y
```
