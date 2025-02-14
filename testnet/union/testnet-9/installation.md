# Installation

### Manuel Installation

#### Setup validator name <a href="#setup-validator-name" id="setup-validator-name"></a>

Replace **VALIDATOR-NAME** with your validator name

```
MONIKER="VALIDATOR-NAME"
```

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
curl -Ls https://go.dev/dl/go1.22.3.linux-amd64.tar.gz | sudo tar -xzf - -C /usr/local
eval $(echo 'export PATH=$PATH:/usr/local/go/bin' | sudo tee /etc/profile.d/golang.sh)
eval $(echo 'export PATH=$PATH:$HOME/go/bin' | tee -a $HOME/.profile)
```

#### Download binaries <a href="#download-binaries" id="download-binaries"></a>

```
# Download project binaries
mkdir -p $HOME/.union/cosmovisor/genesis/bin
wget -O $HOME/.union/cosmovisor/genesis/bin/uniond https://snapshots.kjnodes.com/union-testnet/uniond-v0.25.0-linux-amd64
chmod +x $HOME/.union/cosmovisor/genesis/bin/uniond

# Create application symlinks
ln -s $HOME/.union/cosmovisor/genesis $HOME/.union/cosmovisor/current -f
sudo ln -s $HOME/.union/cosmovisor/current/bin/uniond /usr/local/bin/uniond -f
```

#### Install Cosmovisor and create a service <a href="#install-cosmovisor-and-create-a-service" id="install-cosmovisor-and-create-a-service"></a>

```
# Download and install Cosmovisor
go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@v1.6.0

# Create service
sudo tee /etc/systemd/system/union-testnet.service > /dev/null << EOF
[Unit]
Description=union node service
After=network-online.target

[Service]
User=$USER
ExecStart=$(which cosmovisor) run start --home=$HOME/.union
Restart=on-failure
RestartSec=10
LimitNOFILE=65535
Environment="DAEMON_HOME=$HOME/.union"
Environment="DAEMON_NAME=uniond"
Environment="UNSAFE_SKIP_BACKUP=true"
Environment="PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:$HOME/.union/cosmovisor/current/bin"

[Install]
WantedBy=multi-user.target
EOF
sudo systemctl daemon-reload
sudo systemctl enable union-testnet.service
```

#### Initialize the node <a href="#initialize-the-node" id="initialize-the-node"></a>

```
# Workaround mandatory home argument
alias uniond='uniond --home=$HOME/.union/'

# Set node configuration
uniond config set client chain-id union-testnet-9
uniond config set client keyring-backend test
uniond config set client node tcp://localhost:17157

# Initialize the node
uniond init $MONIKER --chain-id union-testnet-9 --home=$HOME/.union

# Download genesis and addrbook
curl -Ls https://raw.githubusercontent.com/CoinHuntersTR/props/refs/heads/main/union/genesis.json > $HOME/.union/config/genesis.json
curl -Ls https://raw.githubusercontent.com/CoinHuntersTR/props/refs/heads/main/union/addrbook.json > $HOME/.union/config/addrbook.json

# Add seeds
sed -i -e "s|^seeds *=.*|seeds = \"3f472746f46493309650e5a033076689996c8881@union-testnet.rpc.kjnodes.com:17159\"|" $HOME/.union/config/config.toml

# Set minimum gas price
sed -i -e "s|^minimum-gas-prices *=.*|minimum-gas-prices = \"0muno\"|" $HOME/.union/config/app.toml

# Set pruning
sed -i \
  -e 's|^pruning *=.*|pruning = "custom"|' \
  -e 's|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|' \
  -e 's|^pruning-keep-every *=.*|pruning-keep-every = "0"|' \
  -e 's|^pruning-interval *=.*|pruning-interval = "19"|' \
  $HOME/.union/config/app.toml

# Set custom ports
sed -i -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:17158\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:17157\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:17160\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:17156\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":17166\"%" $HOME/.union/config/config.toml
sed -i -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:17117\"%; s%^address = \":8080\"%address = \":17180\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:17190\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:17191\"%; s%:8545%:17145%; s%:8546%:17146%; s%:6065%:17165%" $HOME/.union/config/app.toml
```

#### Download latest chain snapshot <a href="#download-latest-chain-snapshot" id="download-latest-chain-snapshot"></a>

```
curl -L https://snapshots.coinhunterstr.com/testnet/union/snapshot_latest.tar.lz4 | tar -Ilz4 -xf - -C $HOME/.union
[[ -f $HOME/.union/data/upgrade-info.json ]] && cp $HOME/.union/data/upgrade-info.json $HOME/.union/cosmovisor/genesis/upgrade-info.json
```

#### Start service and check the logs <a href="#start-service-and-check-the-logs" id="start-service-and-check-the-logs"></a>

```
sudo systemctl start union-testnet.service && sudo journalctl -u union-testnet.service -f --no-hostname -o cat
```



### Auto Installation

```
bash <(wget -qO- https://raw.githubusercontent.com/CoinHuntersTR/props/main/AutoInstall/union.sh)
```

### Sync Node

> Node ağ ile eşleşmiş olması gerekiyor. Bunun için &#x20;
>
> ```
> uniond status 2>&1 | jq
> ```
>
> komutunu çalıştırdığınızda `false` çıktısı vermesi gerekir. `True` çıktı alırsanız aşağıdaki adımlara devam etmeyin.

### Run a Validator

```
cd $HOME
```

> İlk önce Pubkeyimizi alıyoruz.

```
uniond comet show-validator
```

`{"@type":"/cosmos.crypto.ed25519.PubKey","key":"0LuMdRNJpWGiH+b+................"}` buna benzer bir çıktı alacaksınız.

> Sonrasında validator json dosyası açıyoruz.

> Aşağıdaki dosyayı kendinize göre düzenlemeyi unutmayın. Validator ismi, site linkleri vs.

```
cat << EOF > ~/.union/config/validator.json
{
	"pubkey": {"@type":"/cosmos.crypto.ed25519.PubKey","key":"BCDw/cq+7VxwNU/UIDc08JaYXru0Wa8SPoamzYSHfY8="},
	"amount": "1000000muno",
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
sudo systemctl start union-testnet.service && sudo journalctl -u union-testnet.service -f --no-hostname -o cat
```

> Şimdi aşağıdaki komutu çalıştırıyoruz. `wallet` yerine kendi cüzdan isminizi yazmayı unutmayın. Terminale cüzdan kurmak için `Useful Commands` bölümüne bakabilirsiniz.
>
> İlk olarak aşağıdaki komutları çalıştırıyoruz.

```
export PRIV_KEY=$(jq -r '.priv_key.value' ~/.union/config/priv_validator_key.json)
export POSSESSION_PROOF=$(uniond prove-possession "$PRIV_KEY")
```

```
uniond union-staking create-union-validator /root/.union/config/validator.json $POSSESSION_PROOF \
  --from wallet \
  --chain-id union-testnet-9 \
  --fees 0muno 
```

Sonrasında validator create komutunu çalışıtıryoruz.&#x20;
