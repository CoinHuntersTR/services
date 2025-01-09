# Useful commands

### 🔑 Key management <a href="#key-management" id="key-management"></a>

**ADD NEW KEY**

```
gonative keys add wallet
```

**RECOVER EXISTING KEY**

```
gonative keys add wallet --recover
```

**LIST ALL KEYS**

```
gonative keys list
```

**DELETE KEY**

```
gonative keys delete wallet
```

**EXPORT KEY TO A FILE**

```
gonative keys export wallet
```

**IMPORT KEY FROM A FILE**

```
gonative keys import wallet wallet.backup
```

**QUERY WALLET BALANCE**

```
gonative q bank balances $(gonative keys show wallet -a)
```

### 👷 Validator management <a href="#validator-management" id="validator-management"></a>

Please make sure you have adjusted **moniker**, **identity**, **details** and **website** to match your values.

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

**UNJAIL VALIDATOR**

```
gonative tx slashing unjail --from wallet --chain-id native-t1 --gas-adjustment 1.5 --gas auto --gas-prices 20000untiv -y
```

**JAIL REASON**

```
gonative query slashing signing-info $(gonative tendermint show-validator)
```

**LIST ALL ACTIVE VALIDATORS**

```
gonative q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

**LIST ALL INACTIVE VALIDATORS**

```
gonative q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_UNBONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

**VIEW VALIDATOR DETAILS**

```
gonative q staking validator $(gonative keys show wallet --bech val -a)
```

### 💲 Token management <a href="#token-management" id="token-management"></a>

**WITHDRAW REWARDS FROM ALL VALIDATORS**

```
gonative tx distribution withdraw-rewards $(gonative keys show wallet --bech val -a) --commission --from wallet --chain-id native-t1 --gas-adjustment 1.5 --gas auto --gas-prices 20000untiv -y
```

**WITHDRAW COMMISSION AND REWARDS FROM YOUR VALIDATOR**

```
gonative tx distribution withdraw-rewards $(gonative keys show wallet --bech val -a) --commission --from wallet --chain-id native-t1 --gas-adjustment 1.5 --gas auto --gas-prices 20000untiv -y
```

**DELEGATE TOKENS TO YOURSELF**

```
gonative tx staking delegate $(gonative keys show wallet --bech val -a) 1000000untiv --from wallet --chain-id native-t1 --gas-adjustment 1.5 --gas auto --gas-prices 20000untiv -y
```

**DELEGATE TOKENS TO VALIDATOR**

```
gonative tx staking delegate <TO_VALOPER_ADDRESS> 1000000untiv --from wallet --chain-id native-t1 --gas-adjustment 1.5 --gas auto --gas-prices 20000untiv -y
```

**REDELEGATE TOKENS TO ANOTHER VALIDATOR**

```
gonative tx staking redelegate $(gonative keys show wallet --bech val -a) <TO_VALOPER_ADDRESS> 1000000untiv --from wallet --chain-id native-t1 --gas-adjustment 1.5 --gas auto --gas-prices 20000untiv -y
```

**UNBOND TOKENS FROM YOUR VALIDATOR**

```
gonative tx staking unbond $(gonative keys show wallet --bech val -a) 1000000untiv --from wallet --chain-id native-t1 --gas-adjustment 1.5 --gas auto --gas-prices 20000untiv -y
```

**SEND TOKENS TO THE WALLET**

```
gonative tx bank send wallet <TO_WALLET_ADDRESS> 1000000untiv --from wallet --chain-id native-t1 --gas-adjustment 1.5 --gas auto --gas-prices 20000untiv -y
```

### 🗳 Governance <a href="#governance" id="governance"></a>

**LIST ALL PROPOSALS**

```
gonative query gov proposals
```

**VIEW PROPOSAL BY ID**

```
gonative query gov proposal 1
```

**VOTE ‘YES’**

```
gonative tx gov vote 1 yes --from wallet --chain-id native-t1 --gas-adjustment 1.5 --gas auto --gas-prices 20000untiv -y
```

**VOTE ‘NO’**

```
gonative tx gov vote 1 no --from wallet --chain-id native-t1 --gas-adjustment 1.5 --gas auto --gas-prices 20000untiv -y
```

**VOTE ‘ABSTAIN’**

```
gonative tx gov vote 1 abstain --from wallet --chain-id native-t1 --gas-adjustment 1.5 --gas auto --gas-prices 20000untiv -y
```

**VOTE ‘NOWITHVETO’**

```
gonative tx gov vote 1 NoWithVeto --from wallet --chain-id native-t1 --gas-adjustment 1.5 --gas auto --gas-prices 20000untiv -y
```

### ⚡️ Utility <a href="#utility" id="utility"></a>

**UPDATE PORTS**

```
UPDATE PORTS
CUSTOM_PORT=110
sed -i -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${CUSTOM_PORT}58\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${CUSTOM_PORT}57\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${CUSTOM_PORT}60\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${CUSTOM_PORT}56\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${CUSTOM_PORT}66\"%" $HOME/.gonative/config/config.toml
sed -i -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:${CUSTOM_PORT}17\"%; s%^address = \":8080\"%address = \":${CUSTOM_PORT}80\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${CUSTOM_PORT}90\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${CUSTOM_PORT}91\"%" $HOME/.gonative/config/app.toml
```

**UPDATE INDEXER**

**Disable indexer**

```
sed -i -e 's|^indexer *=.*|indexer = "null"|' $HOME/.gonative/config/config.toml
```

**Enable indexer**

```
sed -i -e 's|^indexer *=.*|indexer = "kv"|' $HOME/.gonative/config/config.toml
```

**UPDATE PRUNING**

```
sed -i \
  -e 's|^pruning *=.*|pruning = "custom"|' \
  -e 's|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|' \
  -e 's|^pruning-keep-every *=.*|pruning-keep-every = "0"|' \
  -e 's|^pruning-interval *=.*|pruning-interval = "19"|' \
  $HOME/.gonative/config/app.toml
```

### 🚨 Maintenance <a href="#maintenance" id="maintenance"></a>

**GET VALIDATOR INFO**

```
gonative status 2>&1 | jq .ValidatorInfo
```

**GET SYNC INFO**

```
gonative status 2>&1 | jq
```

**GET NODE PEER**

```
echo $(gonative tendermint show-node-id)'@'$(curl -s ifconfig.me)':'$(cat $HOME/.gonative/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```

**CHECK IF VALIDATOR KEY IS CORRECT**

```
[[ $(gonative q staking validator $(gonative keys show wallet --bech val -a) -oj | jq -r .consensus_pubkey.key) = $(gonative status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

**GET LIVE PEERS**

```
curl -sS http://localhost:26657/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}'
```

**SET MINIMUM GAS PRICE**

```
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"20000untiv\"/" $HOME/.gonative/config/app.toml
```

**ENABLE PROMETHEUS**

```
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.gonative/config/config.toml
```

**RESET CHAIN DATA**

```
gonative tendermint unsafe-reset-all --keep-addr-book --home $HOME/.gonative --keep-addr-book
```

**REMOVE NODE**

Please, before proceeding with the next step! All chain data will be lost! Make sure you have backed up your **priv\_validator\_key.json**!

```
cd $HOME
sudo systemctl stop gonatived
sudo systemctl disable gonatived
sudo rm /etc/systemd/system/gonatived.service
sudo systemctl daemon-reload
rm -f $(which gonative)
rm -rf $HOME/.gonative
```

### ⚙️ Service Management <a href="#service-management" id="service-management"></a>

**RELOAD SERVICE CONFIGURATION**

```
sudo systemctl daemon-reload
```

**ENABLE SERVICE**

```
sudo systemctl enable gonatived
```

**DISABLE SERVICE**

```
sudo systemctl disable gonatived
```

**START SERVICE**

```
sudo systemctl start gonatived
```

**STOP SERVICE**

```
sudo systemctl stop gonatived
```

**RESTART SERVICE**

```
sudo systemctl restart gonatived
```

**CHECK SERVICE STATUS**

```
sudo systemctl status gonatived
```

**CHECK SERVICE LOGS**

```
sudo journalctl -u gonatived -f --no-hostname -o cat
```
