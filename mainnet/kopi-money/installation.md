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
VER="1.23.3"
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

wallet yerine istediğiniz bir ismi, MONIKER yerine bir validator adı yazmayı unutmayın.

```
echo "export WALLET="wallet"" >> $HOME/.bash_profile
echo "export MONIKER="test"" >> $HOME/.bash_profile
echo "export KOPI_CHAIN_ID="luwak-1"" >> $HOME/.bash_profile
source $HOME/.bash_profile
```

#### Download and build binaries <a href="#download-and-build-binaries" id="download-and-build-binaries"></a>

```
cd $HOME
wget -O kopid https://github.com/kopi-money/kopi/releases/download/v0.6.5.2/kopid-v0.6.5.2-linux-amd64-static
chmod +x $HOME/kopid
mv $HOME/kopid $HOME/go/bin/kopid
```

#### Config init app

```
kopid init $MONIKER --chain-id luwak-1
sed -i \
-e 's/timeout_propose = .*/timeout_propose = "300ms"/' \
-e 's/timeout_propose_delta = .*/timeout_propose_delta = "50ms"/' \
-e 's/timeout_prevote = .*/timeout_prevote = "100ms"/' \
-e 's/timeout_prevote_delta = .*/timeout_prevote_delta = "50ms"/' \
-e 's/timeout_precommit = .*/timeout_precommit = "100ms"/' \
-e 's/timeout_precommit_delta = .*/timeout_precommit_delta = "50ms"/' \
-e 's/timeout_commit = .*/timeout_commit = "500ms"/' \
-e 's/^create_empty_blocks = .*/create_empty_blocks = true/' \
-e 's/^create_empty_blocks_interval = .*/create_empty_blocks_interval = "15s"/' \
-e 's/^timeout_broadcast_tx_commit = .*/timeout_broadcast_tx_commit = "151s"/' \
-e 's/skip_timeout_commit = .*/skip_timeout_commit = false/' \
  $HOME/.kopid/config/config.toml
```

#### Download Genesis and Addrbook

```
wget -O $HOME/.kopid/config/genesis.json https://raw.githubusercontent.com/CoinHuntersTR/props/refs/heads/main/kopi/genesis.json
wget -O $HOME/.kopid/config/addrbook.json https://raw.githubusercontent.com/CoinHuntersTR/props/refs/heads/main/kopi/addrbook.json
```

#### Set seeds and peers

```
# set seeds and peers
SEEDS="ade4d8bc8cbe014af6ebdf3cb7b1e9ad36f412c0@seeds.polkachu.com:27656"
PEERS="637077d431f618181597706810a65c826524fd74@78.46.19.116:27656,441f307717eeb36d39e9ca62d321f3ad46840300@37.27.15.157:39656,5f16a3a8cdae0e07ca28c8078bc9e92ebb42eb27@95.216.13.161:25656,3a16152ab7ecaf462c43c63c72e63c4068240c28@65.21.17.15:15956,30098d171759fba98c6f8540d812502b9c5baaf9@65.108.109.48:5356,a96649c75f8837d5269077ccf6d34c551fe1a577@88.99.137.138:12656,406dfd6b74324722137c38c8a70dfacee45bac01@95.217.141.114:30756,0ad83ee6a5d06bc3092c6b23992eeb086f4bf84f@65.108.71.137:27656,f3d6e39e68673fcf331c3f2022a104ee9ea4bfdb@135.181.108.189:26656,486a5857fc2f97f0bf0e5f39b833fef733323533@145.239.146.143:27656,7708d7d7d38eb39a98427707a0266e974e9c7f40@162.55.220.37:26656,1fd86135df7c89b7544836b5514ddafe2841c7f5@65.109.26.50:26656,2f53870a9c535760a8dadb2f8bca2e060afa78c5@65.108.78.101:15956,85919e3dcc7eec3b64bfdd87657c4fac307c9d23@65.109.34.145:26656,847c3bf157b57cd5a5ea526e7746705fedced702@88.99.68.249:30756,e4c71cecc9d5bc6c70018ce1121336dffdd68827@162.55.97.180:24656,2903938ef4d9180b9d0c587d80e7b32672b28794@158.220.93.183:26056,91c3cdee22cb37ff587ac124406f4306e3bdc010@27.79.170.158:16656,3e84ee2fbca0ebd65e04f49c226408b79b0bb1b5@185.232.70.33:14656,fb44c70b5eca8dca784cf9203f8b9ffdada79ce4@49.13.118.169:26656,95f0431dbb6ce5c8148fb850f19fcd638497cf0a@152.53.87.42:19656,b00fc59914fc4660febc682859cec73204c8cc61@91.233.183.117:38656,b710f68d569b3b01c09608094e6c748964ee185c@167.235.132.211:26656,7ed647a702eadf80bfb64e6777f5ae965115dcc8@65.109.84.235:19656,5f48de8a84290551311a069e9bcb9e22e9b43124@88.99.149.170:45656,fffe2063424bb7fa3f6bcfdd08259c74c59cea2c@95.217.107.137:12656,d7279900d54b4ee9d90fb74c4c4e50ccbae98908@168.119.11.176:39656,509fed0e38aeb7225b33dd55b399a8f6b30853e8@65.108.234.137:27656,a7b9aa429031d4974ef5e5de9658e35b4a1e41ac@65.108.232.254:12020,03e66d02eeca742ce4f54b4f44437af8aa770016@95.217.204.58:27656,a71ac51dc95e60665a15c25ef73e13d4e048a980@95.217.40.175:12656,463d993a60045e9bb07b4ac6b52558acd4d5d5d9@152.53.64.255:39656,86ab755854b538e9f5dca231f6cc245ecf64ca3d@89.117.56.126:26056,f94b5f1d3862280fbdfa3b6c8bb131dfe7ec179c@37.221.198.137:11656,db5e173a098f0a7d5a2c036cfc8cda1091b38234@65.109.18.169:30756,31952feee9dc72da9d281fd47fae87b9422a3245@188.245.230.165:16656,28ee34839a7a33a6a3d6b99ce169295c9b7c5583@195.201.148.131:16656,cb2c613016927cdfed2947e531146b06b274b88a@116.203.224.246:16656,f26f078bb8176b7451259282f59f38368b4d3797@193.34.213.234:11656,2ed7a597d06c1751300c9ba1eae3496c46a3fde3@62.164.217.63:26656,b85358e035343a3b15e77e1102857dcdaf70053b@51.158.206.31:25256"
sed -i -e "/^\[p2p\]/,/^\[/{s/^[[:space:]]*seeds *=.*/seeds = \"$SEEDS\"/}" \
       -e "/^\[p2p\]/,/^\[/{s/^[[:space:]]*persistent_peers *=.*/persistent_peers = \"$PEERS\"/}" \
       $HOME/.kopid/config/config.toml
```

#### Config Pruning

```
pruning="custom"
pruning_keep_recent="1000"
pruning_keep_every="0"
pruning_interval="10"
sed -i -e "s/^pruning *=.*/pruning = \"custom\"/" $HOME/.kopid/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"100\"/" $HOME/.kopid/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"50\"/" $HOME/.kopid/config/app.toml
```

#### set minimum gas price, enable prometheus and disable indexing

```
sed -i 's|minimum-gas-prices =.*|minimum-gas-prices = "0ukopi"|g' $HOME/.kopid/config/app.toml
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.kopid/config/config.toml
sed -i -e "s/^indexer *=.*/indexer = \"null\"/" $HOME/.kopid/config/config.toml
```

#### create service file

```
sudo tee /etc/systemd/system/kopid.service > /dev/null <<EOF
[Unit]
Description=kopi node
After=network-online.target
[Service]
User=$USER
WorkingDirectory=$HOME/.kopid
ExecStart=$(which kopid) start --home $HOME/.kopid
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
sudo systemctl enable kopid.service
sudo systemctl restart kopid.service && sudo journalctl -fu kopid.service -o cat
```

#### Snapshot

#### Stop the service and reset the data <a href="#stop-the-service-and-reset-the-data" id="stop-the-service-and-reset-the-data"></a>

```
sudo systemctl stop kopid.service
cp $HOME/.mantrachain/data/priv_validator_state.json $HOME/.mantrachain/priv_validator_state.json.backup
rm -rf $HOME/.mantrachain/data
```

#### Download latest snapshot <a href="#download-latest-snapshot" id="download-latest-snapshot"></a>

```
curl -L https://snapshots.coinhunterstr.com/mainnet/mantra/snapshot_latest.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.mantrachain
mv $HOME/.mantrachain/priv_validator_state.json.backup $HOME/.mantrachain/data/priv_validator_state.json
```

#### Restart the service and check the log <a href="#restart-the-service-and-check-the-log" id="restart-the-service-and-check-the-log"></a>

```
sudo systemctl restart kopid.service && sudo journalctl -fu kopid.service -o cat
```

## Auto Installation

```
bash <(wget -qO- https://raw.githubusercontent.com/CoinHuntersTR/props/refs/heads/main/AutoInstall/kopi.sh)
```

### Sync Node

> Node ağ ile eşleşmiş olması gerekiyor. Bunun için `kopid status 2>&1 | jq` komutunu çalıştırdığınızda `false` çıktısı vermesi gerekir. `True` çıktı alırsanız aşağıdaki adımlara devam etmeyin.

### Run a Validator

```
cd $HOME
```

> İlk önce Pubkeyimizi alıyoruz.

```
kopid tendermint show-validator
```

`{"@type":"/cosmos.crypto.ed25519.PubKey","key":"0LuMdRNJpWGiH+b+................"}` buna benzer bir çıktı alacaksınız.

> Sonrasında validator json dosyası açıyoruz.

> Aşağıdaki dosyayı kendinize göre düzenlemeyi unutmayın. Validator ismi, site linkleri vs.

```
cat << EOF > ~/validator.json
{   
    "pubkey":{"@type":"/cosmos.crypto.ed25519.PubKey","key":"3NyM9VcMXSDPH0yVwqbo9FKhQ3Nt4hEE7iSYb6jnWQU="},
    "amount": "9000000ukopi",
    "moniker": "",
    "identity": "",
    "website": "",
    "security": "",
    "details": " ",
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
sudo systemctl restart kopid.service && sudo journalctl -fu kopid.service -o cat
```

> Şimdi aşağıdaki komutu çalıştırıyoruz. `wallet` yerine kendi cüzdan isminizi yazmayı unutmayın. Terminalde cüzdan kurmak için `Useful Commands` bölümüne bakabilirsiniz.

```
kopid tx staking create-validator ~/validator.json --from wallet --chain-id luwak-1 --gas="auto" --gas-adjustment 2 --gas-prices="0.01ukopi"
```
