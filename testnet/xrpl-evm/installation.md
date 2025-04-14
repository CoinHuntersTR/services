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
VER="1.23.2"
wget "https://golang.org/dl/go$VER.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$VER.linux-amd64.tar.gz"
rm "go$VER.linux-amd64.tar.gz"
[ ! -f ~/.bash_profile ] && touch ~/.bash_profile
echo "export PATH=$PATH:/usr/local/go/bin:~/go/bin" >> ~/.bash_profile
source $HOME/.bash_profile
[ ! -d ~/go/bin ] && mkdir -p ~/go/bin
```

#### Download and build binaries <a href="#download-and-build-binaries" id="download-and-build-binaries"></a>

```
git clone https://github.com/xrplevm/node xrpl && cd xrpl
git checkout v6.0.0
make install

exrpd version --long | grep -e version -e commit
# version: v6.0.0
# commit: 6688ca628b4787b41c9f8cfe431dd718753f542b
```

#### Config init app

```
exrpd init MONIKERNAME --chain-id xrplevm_1449000-1
```

#### Download Genesis and Addrbook

```
wget -O $HOME/.exrpd/config/genesis.json https://raw.githubusercontent.com/xrplevm/networks/refs/heads/main/testnet/genesis.json
wget -O $HOME/.exrpd/config/addrbook.json https://share102.utsa.tech/xrpl/addrbook.json
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
sed -i.bak -e "s/^chain-id *=.*/chain-id = \"xrplevm_1449000-1\"/;" ~/.exrpd/config/client.toml
sed -i.bak -e "s/^keyring-backend *=.*/keyring-backend = \"os\"/;" ~/.exrpd/config/client.toml
sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.0025axrp\"/;" ~/.exrpd/config/app.toml
external_address=$(wget -qO- eth0.me)
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:26656\"/" $HOME/.exrpd/config/config.toml
PEERS=`curl -sL https://raw.githubusercontent.com/xrplevm/networks/main/testnet/peers.txt | sort -R | head -n 10 | awk '{print $1}' | paste -s -d, -`
sed -i.bak -e "s/^seeds *=.*/seeds = \"$PEERS\"/" ~/.exrpd/config/config.toml
sed -i -e "s/^filter_peers *=.*/filter_peers = \"true\"/" $HOME/.exrpd/config/config.toml
```

#### set minimum gas price, enable prometheus and disable indexing

```
pruning="custom"
pruning_keep_recent="1000"
pruning_interval="100"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.exrpd/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.exrpd/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.exrpd/config/app.toml

indexer="null"
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.exrpd/config/config.toml

snapshot_interval=1000
sed -i.bak -e "s/^snapshot-interval *=.*/snapshot-interval = \"$snapshot_interval\"/" ~/.exrpd/config/app.toml
```

#### create service file

```
tee /etc/systemd/system/exrpd.service > /dev/null <<EOF
[Unit]
Description=exrpd
After=network-online.target

[Service]
User=$USER
ExecStart=$(which exrpd) start
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```

#### enable and start service

```
systemctl daemon-reload
systemctl enable exrpd
systemctl restart exrpd && journalctl -u exrpd -f -o cat
```

#### Snapshot

#### Stop the service and reset the data <a href="#stop-the-service-and-reset-the-data" id="stop-the-service-and-reset-the-data"></a>

```
sudo systemctl stop exrpd
cp $HOME/.exrpd/data/priv_validator_state.json $HOME/.exrpd/priv_validator_state.json.backup
rm -rf $HOME/.exrpd/data
```

#### Download latest snapshot <a href="#download-latest-snapshot" id="download-latest-snapshot"></a>

```
curl -L https://snapshots.coinhunterstr.com/testnet/xrplevm/snapshot_latest.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.exrpd
mv $HOME/.exrpd/priv_validator_state.json.backup $HOME/.exrpd/data/priv_validator_state.json
```

#### Restart the service and check the log <a href="#restart-the-service-and-check-the-log" id="restart-the-service-and-check-the-log"></a>

```
sudo systemctl restart exrpd && sudo journalctl -u exrpd -f --no-hostname -o cat
```

## Auto Installation

```
bash <(wget -qO- https://raw.githubusercontent.com/CoinHuntersTR/props/main/AutoInstall/xrpl.sh)
```

### Sync Node

> Node ağ ile eşleşmiş olması gerekiyor. Bunun için `exrpd status 2>&1 | jq` komutunu çalıştırdığınızda `false` çıktısı vermesi gerekir. `True` çıktı alırsanız aşağıdaki adımlara devam etmeyin.

### Run a Validator

```
cd $HOME
```

> İlk önce Pubkeyimizi alıyoruz.

```
exrpd comet show-validator
```

`{"@type":"/cosmos.crypto.ed25519.PubKey","key":"0LuMdRNJpWGiH+b+................"}` buna benzer bir çıktı alacaksınız.

> Sonrasında validator json dosyası açıyoruz.

> Aşağıdaki dosyayı kendinize göre düzenlemeyi unutmayın. Validator ismi, site linkleri vs.

```
cat << EOF > ~/validator.json
{   
    "pubkey":{"@type":"/cosmos.crypto.ed25519.PubKey","key":"BVTxen+wn6ZgBPUqsgdFDonZ3cr2r+eoYRfF8sTx6kQ="},
    "amount": "1000000uxrp",
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
systemctl restart exrpd && journalctl -u exrpd -f -o cat
```

> Şimdi aşağıdaki komutu çalıştırıyoruz. `wallet` yerine kendi cüzdan isminizi yazmayı unutmayın. Terminale cüzdan kurmak için `Useful Commands` bölümüne bakabilirsiniz.

```
exrpd tx staking create-validator ~/validator.json --from wallet --chain-id xrplevm_1449000-1 --gas="auto" --gas-adjustment 2 --gas-prices="0axrp"
```
