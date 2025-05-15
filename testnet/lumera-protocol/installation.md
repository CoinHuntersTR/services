# Installation

## Manual Installation

#### Install dependencies <a href="#install-dependencies" id="install-dependencies"></a>

**UPDATE SYSTEM AND INSTALL BUILD TOOLS**

```
sudo apt update && sudo apt upgrade -y
sudo apt install curl iptables build-essential git wget jq make gcc nano tmux htop nvme-cli pkg-config libssl-dev libleveldb-dev tar clang bsdmainutils ncdu unzip libleveldb-dev -y
```

**INSTALL GO**

```
cd $HOME
VER="1.23.5"
wget "https://golang.org/dl/go$VER.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$VER.linux-amd64.tar.gz"
rm "go$VER.linux-amd64.tar.gz"
[ ! -f ~/.bash_profile ] && touch ~/.bash_profile
echo "export PATH=$PATH:/usr/local/go/bin:~/go/bin" >> ~/.bash_profile
source $HOME/.bash_profile
[ ! -d ~/go/bin ] && mkdir -p ~/go/bin

# set vars
echo "export WALLET="$WALLET"" >> $HOME/.bash_profile
echo "export MONIKER="$MONIKER"" >> $HOME/.bash_profile
echo "export LUMERA_CHAIN_ID="lumera-testnet-1"" >> $HOME/.bash_profile
echo "export LUMERA_PORT="$PORT"" >> $HOME/.bash_profile
source $HOME/.bash_profile
```

#### Download and build binaries <a href="#download-and-build-binaries" id="download-and-build-binaries"></a>

```
cd $HOME
wget https://github.com/LumeraProtocol/lumera/releases/download/v0.4.1/lumera_v0.4.1_linux_amd64.tar.gz
tar -xvf lumera_v0.4.1_linux_amd64.tar.gz
rm lumera_v0.4.1_linux_amd64.tar.gz
rm -f install.sh
sudo mv libwasmvm.x86_64.so /usr/lib/
chmod +x lumerad
mv lumerad $HOME/go/bin/
lumerad version
```

#### Config init app

```
lumerad init $MONIKER --chain-id $LUMERA_CHAIN_ID
sed -i -e "s|^node *=.*|node = \"tcp://localhost:${LUMERA_PORT}657\"|" $HOME/.lumera/config/client.toml
sed -i -e "s|^keyring-backend *=.*|keyring-backend = \"os\"|" $HOME/.lumera/config/client.toml
sed -i -e "s|^chain-id *=.*|chain-id = \"lumera-testnet-1\"|" $HOME/.lumera/config/client.toml
```

#### Download Genesis and Addrbook

```
curl -Ls https://ss-t.lumera.nodestake.org/genesis.json > $HOME/.lumera/config/genesis.json 
curl -Ls https://ss-t.lumera.nodestake.org/addrbook.json > $HOME/.lumera/config/addrbook.json
```

#### Set seeds and peers

```
SEEDS="6a271a9b7d07393a1521b1c7386a9fa9147a1762@xrplevm-testnet-seed.itrocket.net:16656"
PEERS="166f7e48e1c756cc663fee5be96ab2bbdb4db970@xrplevm-testnet-peer.itrocket.net:13656,d3d73f64abb4e785fd7d4541013b2f7a0b284612@135.181.210.47:56656,edda2d19e6f124fb05a09490d8463670c1e4cdd9@65.109.58.214:26656,727b11452d568d6f09d6378ae1e2718311c288ad@152.53.228.219:26656,5998f89c7549ec10672bf16a4d5b90786e856393@195.3.223.73:22656,c451a651b8d513b3e2cd8724537a80481c8cfdfd@152.53.51.57:13656,a601123b671af68731b9137dac59ab3ca5f1ce29@195.3.223.78:22656,788ee1661ed6f87e19015d4884ab94c51bc36a5f@116.202.210.177:13656,ce425e9ae057c4d34e63284a124404eea7d7b942@95.214.55.184:23656,a4f2d903cebf5bc83fcb66fbda0af5cb922a6436@135.181.139.249:47656,ab41e5911826a692c08ced4d737e905ffb3a6c28@65.108.199.62:56656"
sed -i -e "/^\[p2p\]/,/^\[/{s/^[[:space:]]*seeds *=.*/seeds = \"$SEEDS\"/}" \
       -e "/^\[p2p\]/,/^\[/{s/^[[:space:]]*persistent_peers *=.*/persistent_peers = \"$PEERS\"/}" $HOME/.exrpd/config/config.toml
```

#### Config Pruning

```
# set custom ports in app.toml
sed -i.bak -e "s%:1317%:${LUMERA_PORT}317%g;
s%:8080%:${LUMERA_PORT}080%g;
s%:9090%:${LUMERA_PORT}090%g;
s%:9091%:${LUMERA_PORT}091%g;
s%:8545%:${LUMERA_PORT}545%g;
s%:8546%:${LUMERA_PORT}546%g;
s%:6065%:${LUMERA_PORT}065%g" $HOME/.lumera/config/app.toml

# set custom ports in config.toml file
sed -i.bak -e "s%:26658%:${LUMERA_PORT}658%g;
s%:26657%:${LUMERA_PORT}657%g;
s%:6060%:${LUMERA_PORT}060%g;
s%:26656%:${LUMERA_PORT}656%g;
s%^external_address = \"\"%external_address = \"$(wget -qO- eth0.me):${LUMERA_PORT}656\"%;
s%:26660%:${LUMERA_PORT}660%g" $HOME/.lumera/config/config.toml

```

#### set minimum gas price, enable prometheus and disable indexing

```
# config pruning
sed -i -e "s/^pruning *=.*/pruning = \"custom\"/" $HOME/.lumera/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"100\"/" $HOME/.lumera/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"50\"/" $HOME/.lumera/config/app.toml

# set minimum gas price, enable prometheus and disable indexing
sed -i 's|minimum-gas-prices =.*|minimum-gas-prices = "0.025ulume"|g' $HOME/.lumera/config/app.toml
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.lumera/config/config.toml
sed -i -e "s/^indexer *=.*/indexer = \"null\"/" $HOME/.lumera/config/config.toml
```

#### create service file

```
sudo tee /etc/systemd/system/lumerad.service > /dev/null <<EOF
[Unit]
Description=lumerad Daemon
After=network-online.target
[Service]
User=$USER
ExecStart=$(which lumerad) start
Restart=always
RestartSec=3
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF
```

#### enable and start service

```
sudo systemctl daemon-reload
sudo systemctl enable lumerad
sudo systemctl restart lumerad && sudo journalctl -u lumerad -f
```

#### Snapshot

#### Stop the service and reset the data <a href="#stop-the-service-and-reset-the-data" id="stop-the-service-and-reset-the-data"></a>

<pre><code>sudo systemctl stop lumerad
cp $HOME/.lumera/data/priv_validator_state.json $HOME/.lumera/priv_validator_state.json.backup
rm -rf $HOME/.lumera/data
rm -rf $HOME/.lumera/wasm
lumerad tendermint unsafe-reset-all --home ~/.lumera/ --keep-addr-book
</code></pre>

#### Download latest snapshot <a href="#download-latest-snapshot" id="download-latest-snapshot"></a>

```
curl -L https://snapshots.coinhunterstr.com/testnet/lumera/lumera/lumera_snapshot_latest.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.lumera
mv $HOME/.lumera/priv_validator_state.json.backup $HOME/.lumera/data/priv_validator_state.json
```

#### Restart the service and check the log <a href="#restart-the-service-and-check-the-log" id="restart-the-service-and-check-the-log"></a>

```
sudo systemctl restart lumerad && sudo journalctl -u lumerad -f --no-hostname -o cat
```

## Auto Installation

```
bash <(wget -qO- https://raw.githubusercontent.com/CoinHuntersTR/props/main/AutoInstall/lumera.sh)
```

### Sync Node

> Node ağ ile eşleşmiş olması gerekiyor. Bunun için `exrpd status 2>&1 | jq` komutunu çalıştırdığınızda `false` çıktısı vermesi gerekir. `True` çıktı alırsanız aşağıdaki adımlara devam etmeyin.

### Run a Validator

```
cd $HOME
```

> İlk önce Pubkeyimizi alıyoruz.

```
lumerad comet show-validator
```

`{"@type":"/cosmos.crypto.ed25519.PubKey","key":"0LuMdRNJpWGiH+b+................"}` buna benzer bir çıktı alacaksınız.

> Sonrasında validator json dosyası açıyoruz.

> Aşağıdaki dosyayı kendinize göre düzenlemeyi unutmayın. Validator ismi, site linkleri vs.

```
cat << EOF > ~/validator.json
{   
    "pubkey":{"@type":"/cosmos.crypto.ed25519.PubKey","key":"BVTxen+wn6ZgBPUqsgdFDonZ3cr2r+eoYRfF8sTx6kQ="},
    "amount": "1000000ulume",
    "moniker": "",
    "identity": "",
    "website": "",
    "security": "",
    "details": " Coin Hunters Community ",
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
sudo systemctl restart lumerad && sudo journalctl -u lumerad -f --no-hostname -o cat
```

> Şimdi aşağıdaki komutu çalıştırıyoruz. `wallet` yerine kendi cüzdan isminizi yazmayı unutmayın. Terminale cüzdan kurmak için `Useful Commands` bölümüne bakabilirsiniz.

```
lumerad tx staking create-validator ~/validator.json --from coinhunters --chain-id lumera-testnet-1 --gas="auto" --gas-adjustment 1.4 --gas-prices="0.025ulume"
```
