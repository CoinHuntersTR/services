# Installation

### Manuel Installation

#### 1. Set Environment Variables

```bash
# Set your wallet name  
WALLET="your_wallet_name"  
  
# Set your moniker (node name)  
MONIKER="your_moniker"  
  
# Set custom port (default is 26)  
PORT="17"  # Example port  
  
# Set BLS password  
BLS_PASSWORD="your_bls_password"  
  
# Save variables to bash profile  
echo "export WALLET=\"$WALLET\"" >> $HOME/.bash_profile  
echo "export MONIKER=\"$MONIKER\"" >> $HOME/.bash_profile  
echo "export BABYLON_CHAIN_ID=\"bbn-1\"" >> $HOME/.bash_profile  
echo "export BABYLON_PORT=\"$PORT\"" >> $HOME/.bash_profile  
echo "export BABYLON_BLS_PASSWORD=\"$BLS_PASSWORD\"" >> $HOME/.bash_profile  
source $HOME/.bash_profile  
  
# Create BLS password file  
echo "$BLS_PASSWORD" > $HOME/bls_password.txt  
```

#### 2. Install Go

```bash
cd $HOME  
VER="1.23.4"  
wget "https://golang.org/dl/go$VER.linux-amd64.tar.gz"  
sudo rm -rf /usr/local/go  
sudo tar -C /usr/local -xzf "go$VER.linux-amd64.tar.gz"  
rm "go$VER.linux-amd64.tar.gz"  
[ ! -f ~/.bash_profile ] && touch ~/.bash_profile  
echo "export PATH=$PATH:/usr/local/go/bin:~/go/bin" >> ~/.bash_profile  
source $HOME/.bash_profile  
[ ! -d ~/go/bin ] && mkdir -p ~/go/bin  
  
# Verify Go installation  
go version  
```

#### 3. Install Dependencies

```bash
# Update system  
sudo apt update && sudo apt upgrade -y  
  
# Install build tools  
sudo apt install -y build-essential curl git jq lz4 wget make gcc tmux chrony unzip  
```

#### 4. Install Babylon Binary

```bash
git clone https://github.com/babylonlabs-io/babylon.git babylon  
cd babylon  
git checkout v1.0.1  
make install  
  
# Verify installation  
babylond version  
```

#### 5. Initialize Node

```bash
babylond init $MONIKER --chain-id $BABYLON_CHAIN_ID --home $HOME/.babylond  
```

#### 6. Download Genesis and Addrbook

```bash
wget -O $HOME/.babylond/config/genesis.json https://raw.githubusercontent.com/CoinHuntersTR/props/refs/heads/main/babylon/genesis.json  
wget -O $HOME/.babylond/config/addrbook.json https://raw.githubusercontent.com/CoinHuntersTR/props/refs/heads/main/babylon/addrbook.json  
```

#### 7. Configure Node

```bash
# Set seeds and peers  
SEEDS="42fad8afbf7dfca51020c3c6e1a487ce17c4c218@babylon-seed-1.nodes.guru:55706,ade4d8bc8cbe014af6ebdf3cb7b1e9ad36f412c0@seeds.polkachu.com:20656"  
PEERS="f0d280c08608400cac0ccc3d64d67c63fabc8bcc@91.134.70.52:55706,4c1406cb6867232b7ea130ed3a3d25996ca06844@23.88.6.237:20656,b40a147910a608018c47a0e0225106d00d2651ed@5.9.99.42:20656,184db83783c9158a3e99809ffed3752e180597be@65.108.205.121:20656,1f06b55dfbae181fa40ec08fe145b3caef6d3c83@5.9.81.54:2080,7d728de314f9746e499034bfcfc5a9023c672df5@84.32.32.149:18800"  
  
# Edit config.toml to add seeds and peers  
sed -i -e "/^\[p2p\]/,/^\[/{s/^[[:space:]]*seeds *=.*/seeds = \"$SEEDS\"/}" \  
       -e "/^\[p2p\]/,/^\[/{s/^[[:space:]]*persistent_peers *=.*/persistent_peers = \"$PEERS\"/}" $HOME/.babylond/config/config.toml  
  
# Configure custom ports in app.toml  
sed -i.bak -e "s%:1317%:${BABYLON_PORT}317%g;  
s%:8080%:${BABYLON_PORT}080%g;  
s%:9090%:${BABYLON_PORT}090%g;  
s%:9091%:${BABYLON_PORT}091%g;  
s%:8545%:${BABYLON_PORT}545%g;  
s%:8546%:${BABYLON_PORT}546%g;  
s%:6065%:${BABYLON_PORT}065%g" $HOME/.babylond/config/app.toml  
  
# Configure custom ports in config.toml  
sed -i.bak -e "s%:26658%:${BABYLON_PORT}658%g;  
s%:26657%:${BABYLON_PORT}657%g;  
s%:6060%:${BABYLON_PORT}060%g;  
s%:26656%:${BABYLON_PORT}656%g;  
s%^external_address = \"\"%external_address = \"$(wget -qO- eth0.me):${BABYLON_PORT}656\"%;  
s%:26660%:${BABYLON_PORT}660%g" $HOME/.babylond/config/config.toml  
  
# Configure pruning  
sed -i -e "s/^pruning *=.*/pruning = \"custom\"/" $HOME/.babylond/config/app.toml   
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"100\"/" $HOME/.babylond/config/app.toml  
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"19\"/" $HOME/.babylond/config/app.toml  
  
# Set minimum gas price, enable prometheus and disable indexing  
sed -i 's|minimum-gas-prices =.*|minimum-gas-prices = "0.002ubbn"|g' $HOME/.babylond/config/app.toml  
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.babylond/config/config.toml  
sed -i -e "s/^indexer *=.*/indexer = \"null\"/" $HOME/.babylond/config/config.toml  
  
# Set mempool configuration  
sed -i '/\[mempool\]/,/\[/ s/^max-txs *=.*/max-txs = 0/' $HOME/.babylond/config/app.toml  
  
# Add BTC config  
if ! grep -q "\[btc-config\]" $HOME/.babylond/config/app.toml; then  
  echo -e "\n[btc-config]\nnetwork = \"mainnet\"" >> $HOME/.babylond/config/app.toml  
else  
  sed -i '/\[btc-config\]/,/\[/ s/^network *=.*/network = "mainnet"/' $HOME/.babylond/config/app.toml  
fi  
  
# Set timeout_commit  
sed -i '/\[consensus\]/,/\[/ s/^timeout_commit *=.*/timeout_commit = "9200ms"/' $HOME/.babylond/config/config.toml  
```

#### 8. Create Service File

```bash
sudo tee /etc/systemd/system/babylond.service > /dev/null <<EOF  
[Unit]  
Description=babylond.service  
After=network-online.target  
  
[Service]  
User=$USER  
WorkingDirectory=$HOME/.babylond  
Environment="BABYLON_BLS_PASSWORD=$BLS_PASSWORD"  
ExecStart=$(which babylond) start --home $HOME/.babylond  
Restart=on-failure  
RestartSec=5  
LimitNOFILE=65535  
  
[Install]  
WantedBy=multi-user.target  
EOF  
```

#### 9. Reset Node and Download Snapshot

```bash
# Remove any existing BLS key to avoid decryption issues  
if [ -f $HOME/.babylond/config/bls_key.json ]; then  
  rm $HOME/.babylond/config/bls_key.json  
fi  
  
# Reset node  
babylond tendermint unsafe-reset-all --home $HOME/.babylond  
  
# Download and extract snapshot  
curl https://snapshots.coinhunterstr.com/mainnet/babylon/snapshot_latest.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.babylond  
```

#### 10. Start the Node

```bash
# Reload systemd, enable and start service  
sudo systemctl daemon-reload  
sudo systemctl enable babylond  
sudo systemctl start babylond  
  
# Check logs  
sudo journalctl -u babylond -fo cat  
```

Remember to replace placeholder values like "your\_wallet\_name", "your\_moniker", and "your\_bls\_password" with your actual information.

### Auto Installation

```bash
bash <(wget -qO- https://raw.githubusercontent.com/CoinHuntersTR/props/refs/heads/main/AutoInstall/babylon.sh)
```

### Monitor Your Node

```bash
# Check node status  
babylond status | jq  
  
# Check sync status  
babylond status 2>&1 | jq .SyncInfo  
```

### Create Validator

```
cd $HOME
```

```bash
babylond tendermint show-validator
```

for example: `{"@type":"/cosmos.crypto.ed25519.PubKey","key":"0LuMdRNJpWGiH+b+................"}`  &#x20;

```
cat << EOF > ~/validator.json
{   
    "pubkey":{"@type":"/cosmos.crypto.ed25519.PubKey","key":"wHOuViZC5L6fhOrYzFDAx9ySC2GOW61im6u/mP/Urh0="},
    "amount": "1000000ubbn",
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
sudo systemctl restart babylond && sudo journalctl -u babylond -fo cat  
```

> Şimdi aşağıdaki komutu çalıştırıyoruz. `wallet` yerine kendi cüzdan isminizi yazmayı unutmayın. Terminale cüzdan kurmak için `Useful Commands` bölümüne bakabilirsiniz.

```
babylond tx checkpointing create-validator ~/validator.json --from wallet --chain-id bbn-1 --gas="auto" --gas-adjustment 1.5 --gas-prices="0.005ubbn"
```
