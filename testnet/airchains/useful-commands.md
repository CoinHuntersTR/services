# Useful commands

### 🔑 Key management <a href="#key-management" id="key-management"></a>

**ADD NEW KEY**

```
wardend keys add wallet
```

**RECOVER EXISTING KEY**

```
wardend keys add wallet --recover
```

**LIST ALL KEYS**

```
wardend keys list
```

**DELETE KEY**

```
wardend keys delete wallet
```

**EXPORT KEY TO A FILE**

```
wardend keys export wallet
```

**IMPORT KEY FROM A FILE**

```
wardend keys import wallet wallet.backup
```

**QUERY WALLET BALANCE**

```
wardend q bank balances $(wardend keys show wallet -a)
```

### 👷 Validator management <a href="#validator-management" id="validator-management"></a>

Please make sure you have adjusted **moniker**, **identity**, **details** and **website** to match your values.

```
cd $HOME
# Create validator.json file
echo "{\"pubkey\":{\"@type\":\"/cosmos.crypto.ed25519.PubKey\",\"key\":\"$(wardend comet show-validator | grep -Po '\"key\":\s*\"\K[^"]*')\"},
    \"amount\": \"1000000uward\",
    \"moniker\": \"test\",
    \"identity\": \"\",
    \"website\": \"\",
    \"security\": \"\",
    \"details\": \"I love blockchain ❤️\",
    \"commission-rate\": \"0.1\",
    \"commission-max-rate\": \"0.2\",
    \"commission-max-change-rate\": \"0.01\",
    \"min-self-delegation\": \"1\"
}" > validator.json
# Create a validator using the JSON configuration
wardend tx staking create-validator validator.json \
    --from wallet \
    --chain-id buenavista-1 \
	--gas auto --gas-adjustment 1.5 --fees 600uward \
```

**UNJAIL VALIDATOR**

```
wardend tx slashing unjail --from wallet --chain-id buenavista-1 --gas-adjustment 1.5 --gas auto --gas-prices 600uward -y
```

**JAIL REASON**

```
wardend query slashing signing-info $(wardend tendermint show-validator)
```

**LIST ALL ACTIVE VALIDATORS**

```
wardend q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

**LIST ALL INACTIVE VALIDATORS**

```
wardend q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_UNBONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

**VIEW VALIDATOR DETAILS**

```
wardend q staking validator $(wardend keys show wallet --bech val -a)
```

### 💲 Token management <a href="#token-management" id="token-management"></a>

**WITHDRAW REWARDS FROM ALL VALIDATORS**

```
wardend tx distribution withdraw-rewards $(wardend keys show wallet --bech val -a) --commission --from wallet --chain-id buenavista-1 --gas-adjustment 1.5 --gas auto --gas-prices 600uward -y
```

**WITHDRAW COMMISSION AND REWARDS FROM YOUR VALIDATOR**

```
wardend tx distribution withdraw-rewards $(wardend keys show wallet --bech val -a) --commission --from wallet --chain-id buenavista-1 --gas-adjustment 1.5 --gas auto --gas-prices 600uward -y
```

**DELEGATE TOKENS TO YOURSELF**

```
wardend tx staking delegate $(wardend keys show wallet --bech val -a) 90000uward --from wallet --chain-id buenavista-1 --gas-adjustment 1.5 --gas auto --gas-prices 600uward -y
```

**DELEGATE TOKENS TO VALIDATOR**

```
wardend tx staking delegate <TO_VALOPER_ADDRESS> 90000uward --from wallet --chain-id buenavista-1 --gas-adjustment 1.5 --gas auto --gas-prices 600uward -y
```

**REDELEGATE TOKENS TO ANOTHER VALIDATOR**

```
wardend tx staking redelegate $(wardend keys show wallet --bech val -a) <TO_VALOPER_ADDRESS> 90000uward --from wallet --chain-id buenavista-1 --gas-adjustment 1.5 --gas auto --gas-prices 600uward -y
```

**UNBOND TOKENS FROM YOUR VALIDATOR**

```
wardend tx staking unbond $(wardend keys show wallet --bech val -a) 90000uward --from wallet --chain-id buenavista-1 --gas-adjustment 1.5 --gas auto --gas-prices 600uward -y
```

**SEND TOKENS TO THE WALLET**

```
wardend tx bank send wallet <TO_WALLET_ADDRESS> 90000uward --from wallet --chain-id buenavista-1 --gas-adjustment 1.5 --gas auto --gas-prices 600uward -y
```

### 🗳 Governance <a href="#governance" id="governance"></a>

**LIST ALL PROPOSALS**

```
wardend query gov proposals
```

**VIEW PROPOSAL BY ID**

```
wardend query gov proposal 1
```

**VOTE ‘YES’**

```
wardend tx gov vote 1 yes --from wallet --chain-id buenavista-1 --gas-adjustment 1.5 --gas auto --gas-prices 600uward -y
```

**VOTE ‘NO’**

```
wardend tx gov vote 1 no --from wallet --chain-id buenavista-1 --gas-adjustment 1.5 --gas auto --gas-prices 600uward -y
```

**VOTE ‘ABSTAIN’**

```
wardend tx gov vote 1 abstain --from wallet --chain-id buenavista-1 --gas-adjustment 1.5 --gas auto --gas-prices 600uward -y
```

**VOTE ‘NOWITHVETO’**

```
wardend tx gov vote 1 NoWithVeto --from wallet --chain-id buenavista-1 --gas-adjustment 1.5 --gas auto --gas-prices 600uward -y
```

### ⚡️ Utility <a href="#utility" id="utility"></a>

**UPDATE PORTS**

```
UPDATE PORTS
CUSTOM_PORT=110
sed -i -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${CUSTOM_PORT}58\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${CUSTOM_PORT}57\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${CUSTOM_PORT}60\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${CUSTOM_PORT}56\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${CUSTOM_PORT}66\"%" $HOME/.warden/config/config.toml
sed -i -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:${CUSTOM_PORT}17\"%; s%^address = \":8080\"%address = \":${CUSTOM_PORT}80\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${CUSTOM_PORT}90\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${CUSTOM_PORT}91\"%" $HOME/.warden/config/app.toml
```

**UPDATE INDEXER**

**Disable indexer**

```
sed -i -e 's|^indexer *=.*|indexer = "null"|' $HOME/.warden/config/config.toml
```

**Enable indexer**

```
sed -i -e 's|^indexer *=.*|indexer = "kv"|' $HOME/.warden/config/config.toml
```

**UPDATE PRUNING**

```
sed -i \
  -e 's|^pruning *=.*|pruning = "custom"|' \
  -e 's|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|' \
  -e 's|^pruning-keep-every *=.*|pruning-keep-every = "0"|' \
  -e 's|^pruning-interval *=.*|pruning-interval = "19"|' \
  $HOME/.warden/config/app.toml
```

### 🚨 Maintenance <a href="#maintenance" id="maintenance"></a>

**GET VALIDATOR INFO**

```
wardend status 2>&1 | jq .ValidatorInfo
```

**GET SYNC INFO**

```
wardend status 2>&1 | jq .SyncInfo
```

**GET NODE PEER**

```
echo $(wardend tendermint show-node-id)'@'$(curl -s ifconfig.me)':'$(cat $HOME/.warden/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```

**CHECK IF VALIDATOR KEY IS CORRECT**

```
[[ $(wardend q staking validator $(wardend keys show wallet --bech val -a) -oj | jq -r .consensus_pubkey.key) = $(wardend status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

**GET LIVE PEERS**

```
curl -sS http://localhost:14657/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}'
```

**SET MINIMUM GAS PRICE**

```
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"600uward\"/" $HOME/.warden/config/app.toml
```

**ENABLE PROMETHEUS**

```
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.warden/config/config.toml
```

**RESET CHAIN DATA**

```
wardend tendermint unsafe-reset-all --keep-addr-book --home $HOME/.warden --keep-addr-book
```

**REMOVE NODE**

Please, before proceeding with the next step! All chain data will be lost! Make sure you have backed up your **priv\_validator\_key.json**!

```
cd $HOME
sudo systemctl stop wardend
sudo systemctl disable wardend
sudo rm /etc/systemd/system/wardend
sudo systemctl daemon-reload
rm -f $(which wardend)
rm -rf $HOME/.warden
rm -rf $HOME/warden
```

### ⚙️ Service Management <a href="#service-management" id="service-management"></a>

**RELOAD SERVICE CONFIGURATION**

```
sudo systemctl daemon-reload
```

**ENABLE SERVICE**

```
sudo systemctl enable wardend
```

**DISABLE SERVICE**

```
sudo systemctl disable wardend
```

**START SERVICE**

```
sudo systemctl start wardend
```

**STOP SERVICE**

```
sudo systemctl stop wardend
```

**RESTART SERVICE**

```
sudo systemctl restart wardend
```

**CHECK SERVICE STATUS**

```
sudo systemctl status wardend
```

**CHECK SERVICE LOGS**

```
sudo journalctl -u wardend -f --no-hostname -o cat
```
