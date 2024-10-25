# Installation

## Manual Installation

#### Install dependencies <a href="#install-dependencies" id="install-dependencies"></a>

**UPDATE SYSTEM AND INSTALL BUILD TOOLS**

```
sudo apt update && sudo apt upgrade -y
sudo apt install curl tar wget clang pkg-config libssl-dev jq build-essential bsdmainutils git make ncdu gcc git jq chrony liblz4-tool -y
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

### Set Vars

WALLET yerine istediğiniz bir ismi, MONIKER yerine bir validator adı yazmayı unutmayın.

```
echo "export WALLET="wallet"" >> $HOME/.bash_profile
echo "export MONIKER="test"" >> $HOME/.bash_profile
echo "export WARDEN_CHAIN_ID="chiado_10010-1"" >> $HOME/.bash_profile
echo "export WARDEN_PORT="18"" >> $HOME/.bash_profile
source $HOME/.bash_profile
```

#### Download and build binaries <a href="#download-and-build-binaries" id="download-and-build-binaries"></a>

```
cd $HOME
rm -rf bin
mkdir bin && cd bin
wget https://github.com/warden-protocol/wardenprotocol/releases/download/v0.5.2/wardend_Linux_x86_64.zip
unzip wardend_Linux_x86_64.zip
chmod +x wardend
mv $HOME/bin/wardend $HOME/go/bin
```

#### Config init app

```
wardend init $MONIKER
sed -i -e "s|^node *=.*|node = \"tcp://localhost:${WARDEN_PORT}657\"|" $HOME/.warden/config/client.toml
```

#### Download Genesis and Addrbook

```
wget -O $HOME/.warden/config/genesis.json https://raw.githubusercontent.com/CoinHuntersTR/props/refs/heads/main/warden/Chiado/genesis.json
wget -O $HOME/.warden/config/addrbook.json  https://raw.githubusercontent.com/CoinHuntersTR/props/refs/heads/main/warden/Chiado/addrbook.json
```

#### Set seeds and peers

```
# set seeds and peers
URL="https://warden-testnet-rpc.itrocket.net/net_info"
response=$(curl -s $URL)
PEERS=$(echo $response | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):" + (.node_info.listen_addr | capture("(?<ip>.+):(?<port>[0-9]+)$").port)' | paste -sd "," -)
echo "PEERS=\"$PEERS\""

# Update the persistent_peers in the config.toml file
sed -i -e "s|^seeds *=.*|seeds = \"$SEEDS\"|; s|^persistent_peers *=.*|persistent_peers = \"$PEERS\"|" $HOME/.warden/config/config.toml
```

#### Config Pruning

```
sed -i -e "s/^pruning *=.*/pruning = \"custom\"/" $HOME/.warden/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"100\"/" $HOME/.warden/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"50\"/" $HOME/.warden/config/app.toml
```

#### set minimum gas price, enable prometheus and disable indexing

```
sed -i 's|minimum-gas-prices =.*|minimum-gas-prices = "25000000award"|g' $HOME/.warden/config/app.toml
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.warden/config/config.toml
sed -i -e "s/^indexer *=.*/indexer = \"null\"/" $HOME/.warden/config/config.toml
```

#### create service file

```
sudo tee /etc/systemd/system/wardend.service > /dev/null <<EOF
[Unit]
Description=Warden node
After=network-online.target
[Service]
User=$USER
WorkingDirectory=$HOME/.warden
ExecStart=$(which wardend) start --home $HOME/.warden
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
sudo systemctl enable wardend
sudo systemctl restart wardend && sudo journalctl -u wardend -f
```

#### Snapshot

#### Stop the service and reset the data <a href="#stop-the-service-and-reset-the-data" id="stop-the-service-and-reset-the-data"></a>

```
sudo systemctl stop mantrachaind
cp $HOME/.warden/data/priv_validator_state.json $HOME/.warden/priv_validator_state.json.backup
rm -rf $HOME/.warden/data
```

#### Download latest snapshot <a href="#download-latest-snapshot" id="download-latest-snapshot"></a>

```
curl -L https://server-4.itrocket.net/testnet/warden/warden_2024-10-25_32787_snap.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.warden
mv $HOME/.warden/priv_validator_state.json.backup $HOME/.warden/data/priv_validator_state.json
```

#### Restart the service and check the log <a href="#restart-the-service-and-check-the-log" id="restart-the-service-and-check-the-log"></a>

```
sudo systemctl restart wardend && sudo journalctl -u wardend -f
```

## Auto Installation

```
bash <(wget -qO- https://raw.githubusercontent.com/CoinHuntersTR/props/main/AutoInstall/warden.sh)
```

### Faucet

Warden Protocol [Faucet](https://faucet.chiado.wardenprotocol.org/)

### Sync Node

> Node ağ ile eşleşmiş olması gerekiyor. Bunun için `mantrachaind status 2>&1 | jq` komutunu çalıştırdığınızda `false` çıktısı vermesi gerekir. `True` çıktı alırsanız aşağıdaki adımlara devam etmeyin.

### Run a Validator

```
cd $HOME
```

> İlk önce Pubkeyimizi alıyoruz.

```
wardend tendermint show-validator
```

`{"@type":"/cosmos.crypto.ed25519.PubKey","key":"0LuMdRNJpWGiH+b+................"}` buna benzer bir çıktı alacaksınız.

> Sonrasında validator json dosyası açıyoruz.

> Aşağıdaki dosyayı kendinize göre düzenlemeyi unutmayın. Validator ismi, site linkleri vs.

```
cat << EOF > ~/validator.json
{   
    "pubkey":{"@type":"/cosmos.crypto.ed25519.PubKey","key":"BVTxen+wn6ZgBPUqsgdFDonZ3cr2r+eoYRfF8sTx6kQ="},
    "amount": "1000000000000000000award",
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
sudo systemctl restart wardend && sudo journalctl -u wardend -f
```

> Şimdi aşağıdaki komutu çalıştırıyoruz. `wallet` yerine kendi cüzdan isminizi yazmayı unutmayın. Terminale cüzdan kurmak için `Useful Commands` bölümüne bakabilirsiniz.

```
wardend tx staking create-validator ~/validator.json --from wallet --chain-id chiado_10010-1 --gas="auto" --gas-adjustment 2 --gas-prices="25000000award"
```
