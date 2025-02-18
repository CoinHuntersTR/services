# Useful commands

### 🔑 Key management <a href="#key-management" id="key-management"></a>

**ADD NEW KEY**

```
fuelsequencerd keys add wallet
```

**RECOVER EXISTING KEY**

```
fuelsequencerd keys add wallet --recover
```

**LIST ALL KEYS**

```
fuelsequencerd keys list
```

**DELETE KEY**

```
fuelsequencerd keys delete wallet
```

**EXPORT KEY TO A FILE**

```
fuelsequencerd keys export wallet
```

**IMPORT KEY FROM A FILE**

```
fuelsequencerd keys import wallet wallet.backup
```

**QUERY WALLET BALANCE**

```
fuelsequencerd q bank balances $(fuelsequencerd keys show wallet -a)
```

### 👷 Validator management <a href="#validator-management" id="validator-management"></a>

Please make sure you have adjusted **moniker**, **identity**, **details** and **website** to match your values.

**CREATE NEW VALIDATOR**

```
cat << EOF > ~/.fuelsequencer/config/validator.json
{
	"pubkey": ,
	"amount": "1000000fuel",
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

```
fuelsequencerd tx staking create-validator ~/.fuelsequencer/config/validator.json --from wallet --chain-id seq-mainnet-1 --fees 3000000fuel
```

**UNJAIL VALIDATOR**

```
fuelsequencerd tx slashing unjail --from wallet --chain-id seq-mainnet-1 --gas-adjustment 1.4 --gas auto --gas-prices 3000000fuel -y
```

**JAIL REASON**

```
fuelsequencerd query slashing signing-info $(fuelsequencerd tendermint show-validator)
```

**LIST ALL ACTIVE VALIDATORS**

```
fuelsequencerd q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

**LIST ALL INACTIVE VALIDATORS**

```
fuelsequencerd q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_UNBONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

**VIEW VALIDATOR DETAILS**

```
fuelsequencerd q staking validator $(fuelsequencerd keys show wallet --bech val -a)
```

### 💲 Token management <a href="#token-management" id="token-management"></a>

**WITHDRAW REWARDS FROM ALL VALIDATORS**

```
fuelsequencerd tx distribution withdraw-all-rewards --from wallet --chain-id seq-mainnet-1 --gas-adjustment 1.4 --gas auto --gas-prices 3000000fuel -y
```

**WITHDRAW COMMISSION AND REWARDS FROM YOUR VALIDATOR**

```
fuelsequencerd tx distribution withdraw-rewards $(fuelsequencerd keys show wallet --bech val -a) --commission --from wallet --chain-id seq-mainnet-1 --gas-adjustment 1.4 --gas auto --gas-prices 3000000fuel -y
```

**DELEGATE TOKENS TO YOURSELF**

```
fuelsequencerd tx staking delegate $(fuelsequencerd keys show wallet --bech val -a) 1000000fuel --from wallet --chain-id seq-mainnet-1 --gas-adjustment 1.4 --gas auto --gas-prices 3000000fuel -y
```

**DELEGATE TOKENS TO VALIDATOR**

```
fuelsequencerd tx staking delegate <TO_VALOPER_ADDRESS> 1000000fuel --from wallet --chain-id seq-mainnet-1 --gas-adjustment 1.4 --gas auto --gas-prices 3000000fuel -y
```

**REDELEGATE TOKENS TO ANOTHER VALIDATOR**

```
fuelsequencerd tx staking redelegate $(fuelsequencerd keys show wallet --bech val -a) <TO_VALOPER_ADDRESS> 1000000fuel --from wallet --chain-id seq-mainnet-1 --gas-adjustment 1.4 --gas auto --gas-prices 3000000fuel -y
```

**UNBOND TOKENS FROM YOUR VALIDATOR**

```
fuelsequencerd tx staking unbond $(fuelsequencerd keys show wallet --bech val -a) 1000000fuel --from wallet --chain-id seq-mainnet-1 --gas-adjustment 1.4 --gas auto --gas-prices 3000000fuel -y
```

**SEND TOKENS TO THE WALLET**

```
fuelsequencerd tx bank send wallet <TO_WALLET_ADDRESS> 1000000fuel --from wallet --chain-id seq-mainnet-1 --gas-adjustment 1.4 --gas auto --gas-prices 3000000fuel -y
```

### 🗳 Governance <a href="#governance" id="governance"></a>

**LIST ALL PROPOSALS**

```
fuelsequencerd query gov proposals
```

**VIEW PROPOSAL BY ID**

```
fuelsequencerd query gov proposal 1
```

**VOTE ‘YES’**

```
fuelsequencerd tx gov vote 1 yes --from wallet --chain-id seq-mainnet-1 --gas-adjustment 1.4 --gas auto --gas-prices 3000000fuel -y
```

**VOTE ‘NO’**

```
fuelsequencerd tx gov vote 1 no --from wallet --chain-id seq-mainnet-1 --gas-adjustment 1.4 --gas auto --gas-prices 3000000fuel -y
```

**VOTE ‘ABSTAIN’**

```
fuelsequencerd tx gov vote 1 abstain --from wallet --chain-id seq-mainnet-1 --gas-adjustment 1.4 --gas auto --gas-prices 3000000fuel -y
```

**VOTE ‘NOWITHVETO’**

```
fuelsequencerd tx gov vote 1 NoWithVeto --from wallet --chain-id seq-mainnet-1 --gas-adjustment 1.4 --gas auto --gas-prices 3000000fuel -y
```

### ⚡️ Utility <a href="#utility" id="utility"></a>

**UPDATE PORTS**

```
CUSTOM_PORT=110
sed -i -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${CUSTOM_PORT}58\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${CUSTOM_PORT}57\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${CUSTOM_PORT}60\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${CUSTOM_PORT}56\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${CUSTOM_PORT}66\"%" $HOME/.fuelsequencer/config/config.toml
sed -i -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:${CUSTOM_PORT}17\"%; s%^address = \":8080\"%address = \":${CUSTOM_PORT}80\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${CUSTOM_PORT}90\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${CUSTOM_PORT}91\"%" $HOME/.fuelsequencer/config/app.toml
```

**UPDATE INDEXER**

**Disable indexer**

```
sed -i -e 's|^indexer *=.*|indexer = "null"|' $HOME/.fuelsequencer/config/config.toml
```

**Enable indexer**

```
sed -i -e 's|^indexer *=.*|indexer = "kv"|' $HOME/.fuelsequencer/config/config.toml
```

**UPDATE PRUNING**

```
sed -i \
  -e 's|^pruning *=.*|pruning = "custom"|' \
  -e 's|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|' \
  -e 's|^pruning-keep-every *=.*|pruning-keep-every = "0"|' \
  -e 's|^pruning-interval *=.*|pruning-interval = "19"|' \
  $HOME/.fuelsequencer/config/app.toml
```

### 🚨 Maintenance <a href="#maintenance" id="maintenance"></a>

**GET VALIDATOR INFO**

```
fuelsequencerd status 2>&1 | jq .ValidatorInfo
```

**GET SYNC INFO**

```
fuelsequencerd status 2>&1 | jq .SyncInfo
```

**GET NODE PEER**

```
echo $(fuelsequencerd tendermint show-node-id)'@'$(curl -s ifconfig.me)':'$(cat $HOME/.fuelsequencer/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```

**CHECK IF VALIDATOR KEY IS CORRECT**

```
[[ $(fuelsequencerd q staking validator $(fuelsequencerd keys show wallet --bech val -a) -oj | jq -r .consensus_pubkey.key) = $(fuelsequencerd status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

**GET LIVE PEERS**

```
curl -sS http://localhost:26657/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}'
```

**SET MINIMUM GAS PRICE**

```
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"3000000fuel\"/" $HOME/.fuelsequencer/config/app.toml
```

**ENABLE PROMETHEUS**

```
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.fuelsequencer/config/config.toml
```

**RESET CHAIN DATA**

```
fuelsequencerd tendermint unsafe-reset-all --keep-addr-book --home $HOME/.fuelsequencer --keep-addr-book
```

**REMOVE NODE**

Please, before proceeding with the next step! All chain data will be lost! Make sure you have backed up your **priv\_validator\_key.json**!

```
cd $HOME
sudo systemctl stop fuelsequencerd
sudo systemctl disable fuelsequencerd
sudo rm /etc/systemd/system/fuelsequencerd.service
sudo systemctl daemon-reload
rm -f $(which fuelsequencerd)
rm -rf $HOME/.fuelsequencer
```

### ⚙️ Service Management <a href="#service-management" id="service-management"></a>

**RELOAD SERVICE CONFIGURATION**

```
sudo systemctl daemon-reload
```

**ENABLE SERVICE**

```
sudo systemctl enable fuelsequencerd
```

**DISABLE SERVICE**

```
sudo systemctl disable fuelsequencerd
```

**START SERVICE**

```
sudo systemctl start fuelsequencerd
```

**STOP SERVICE**

```
sudo systemctl stop fuelsequencerd
```

**RESTART SERVICE**

```
sudo systemctl restart fuelsequencerd
```

**CHECK SERVICE STATUS**

```
sudo systemctl status fuelsequencerd
```

**CHECK SERVICE LOGS**

```
sudo journalctl -u fuelsequencerd -f --no-hostname -o cat
```
