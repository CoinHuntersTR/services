# Useful commands

### 🔑 Key management <a href="#key-management" id="key-management"></a>

**ADD NEW KEY**

```
lavad keys add wallet
```

**RECOVER EXISTING KEY**

```
lavad keys add wallet --recover
```

**LIST ALL KEYS**

```
lavad keys list
```

**DELETE KEY**

```
lavad keys delete wallet
```

**EXPORT KEY TO A FILE**

```
lavad keys export wallet
```

**IMPORT KEY FROM A FILE**

```
lavad keys import wallet wallet.backup
```

**QUERY WALLET BALANCE**

```
lavad q bank balances $(lavad keys show wallet -a)
```

### 👷 Validator management <a href="#validator-management" id="validator-management"></a>

Please make sure you have adjusted **moniker**, **identity**, **details** and **website** to match your values.

**CREATE NEW VALIDATOR**

```
lavad tx staking create-validator \
--amount 1000000.000000001ulava \
--pubkey $(lavad tendermint show-validator) \
--moniker "YOUR_MONIKER_NAME" \
--identity "YOUR_KEYBASE_ID" \
--details "YOUR_DETAILS" \
--website "YOUR_WEBSITE_URL" \
--chain-id lava-mainnet-1 \
--commission-rate 0.05 \
--commission-max-rate 0.20 \
--commission-max-change-rate 0.05 \
--min-self-delegation 1 \
--from wallet \
--gas-adjustment 1.4 \
--gas auto \
--gas-prices 0.000000001ulava \
-y
```

**EDIT EXISTING VALIDATOR**

```
lavad tx staking edit-validator \
--new-moniker "YOUR_MONIKER_NAME" \
--identity "YOUR_KEYBASE_ID" \
--details "YOUR_DETAILS" \
--website "YOUR_WEBSITE_URL" \
--chain-id lava-mainnet-1 \
--commission-rate 0.05 \
--from wallet \
--gas-adjustment 1.4 \
--gas auto \
--gas-prices 0.000000001ulava \
-y
```

**UNJAIL VALIDATOR**

```
lavad tx slashing unjail --from wallet --chain-id lava-mainnet-1 --gas-adjustment 1.4 --gas auto --gas-prices 0.000000001ulava -y
```

**JAIL REASON**

```
lavad query slashing signing-info $(lavad tendermint show-validator)
```

**LIST ALL ACTIVE VALIDATORS**

```
lavad q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

**LIST ALL INACTIVE VALIDATORS**

```
lavad q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_UNBONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

**VIEW VALIDATOR DETAILS**

```
lavad q staking validator $(lavad keys show wallet --bech val -a)
```

### 💲 Token management <a href="#token-management" id="token-management"></a>

**WITHDRAW REWARDS FROM ALL VALIDATORS**

```
lavad tx distribution withdraw-all-rewards --from wallet --chain-id lava-mainnet-1 --gas-adjustment 1.4 --gas auto --gas-prices 0.000000001ulava -y
```

**WITHDRAW COMMISSION AND REWARDS FROM YOUR VALIDATOR**

```
lavad tx distribution withdraw-rewards $(lavad keys show wallet --bech val -a) --commission --from wallet --chain-id lava-mainnet-1 --gas-adjustment 1.4 --gas auto --gas-prices 0.000000001ulava -y
```

**DELEGATE TOKENS TO YOURSELF**

```
lavad tx staking delegate $(lavad keys show wallet --bech val -a) 1000000.000000001ulava --from wallet --chain-id lava-mainnet-1 --gas-adjustment 1.4 --gas auto --gas-prices 0.000000001ulava -y
```

**DELEGATE TOKENS TO VALIDATOR**

```
lavad tx staking delegate <TO_VALOPER_ADDRESS> 1000000.000000001ulava --from wallet --chain-id lava-mainnet-1 --gas-adjustment 1.4 --gas auto --gas-prices 0.000000001ulava -y
```

**REDELEGATE TOKENS TO ANOTHER VALIDATOR**

```
lavad tx staking redelegate $(lavad keys show wallet --bech val -a) <TO_VALOPER_ADDRESS> 1000000.000000001ulava --from wallet --chain-id lava-mainnet-1 --gas-adjustment 1.4 --gas auto --gas-prices 0.000000001ulava -y
```

**UNBOND TOKENS FROM YOUR VALIDATOR**

```
lavad tx staking unbond $(lavad keys show wallet --bech val -a) 1000000.000000001ulava --from wallet --chain-id lava-mainnet-1 --gas-adjustment 1.4 --gas auto --gas-prices 0.000000001ulava -y
```

**SEND TOKENS TO THE WALLET**

```
lavad tx bank send wallet <TO_WALLET_ADDRESS> 1000000.000000001ulava --from wallet --chain-id lava-mainnet-1 --gas-adjustment 1.4 --gas auto --gas-prices 0.000000001ulava -y
```

### 🗳 Governance <a href="#governance" id="governance"></a>

**LIST ALL PROPOSALS**

```
lavad query gov proposals
```

**VIEW PROPOSAL BY ID**

```
lavad query gov proposal 1
```

**VOTE ‘YES’**

```
lavad tx gov vote 1 yes --from wallet --chain-id lava-mainnet-1 --gas-adjustment 1.4 --gas auto --gas-prices 0.000000001ulava -y
```

**VOTE ‘NO’**

```
lavad tx gov vote 1 no --from wallet --chain-id lava-mainnet-1 --gas-adjustment 1.4 --gas auto --gas-prices 0.000000001ulava -y
```

**VOTE ‘ABSTAIN’**

```
lavad tx gov vote 1 abstain --from wallet --chain-id lava-mainnet-1 --gas-adjustment 1.4 --gas auto --gas-prices 0.000000001ulava -y
```

**VOTE ‘NOWITHVETO’**

```
lavad tx gov vote 1 NoWithVeto --from wallet --chain-id lava-mainnet-1 --gas-adjustment 1.4 --gas auto --gas-prices 0.000000001ulava -y
```

### ⚡️ Utility <a href="#utility" id="utility"></a>

**UPDATE PORTS**

```
CUSTOM_PORT=110
sed -i -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${CUSTOM_PORT}58\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${CUSTOM_PORT}57\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${CUSTOM_PORT}60\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${CUSTOM_PORT}56\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${CUSTOM_PORT}66\"%" $HOME/.lava/config/config.toml
sed -i -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:${CUSTOM_PORT}17\"%; s%^address = \":8080\"%address = \":${CUSTOM_PORT}80\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${CUSTOM_PORT}90\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${CUSTOM_PORT}91\"%" $HOME/.lava/config/app.toml
```

**UPDATE INDEXER**

**Disable indexer**

```
sed -i -e 's|^indexer *=.*|indexer = "null"|' $HOME/.lava/config/config.toml
```

**Enable indexer**

```
sed -i -e 's|^indexer *=.*|indexer = "kv"|' $HOME/.lava/config/config.toml
```

**UPDATE PRUNING**

```
sed -i \
  -e 's|^pruning *=.*|pruning = "custom"|' \
  -e 's|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|' \
  -e 's|^pruning-keep-every *=.*|pruning-keep-every = "0"|' \
  -e 's|^pruning-interval *=.*|pruning-interval = "19"|' \
  $HOME/.lava/config/app.toml
```

### 🚨 Maintenance <a href="#maintenance" id="maintenance"></a>

**GET VALIDATOR INFO**

```
lavad status 2>&1 | jq .ValidatorInfo
```

**GET SYNC INFO**

```
lavad status 2>&1 | jq .SyncInfo
```

**GET NODE PEER**

```
echo $(lavad tendermint show-node-id)'@'$(curl -s ifconfig.me)':'$(cat $HOME/.lava/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```

**CHECK IF VALIDATOR KEY IS CORRECT**

```
[[ $(lavad q staking validator $(lavad keys show wallet --bech val -a) -oj | jq -r .consensus_pubkey.key) = $(lavad status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

**GET LIVE PEERS**

```
curl -sS http://localhost:14457/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}'
```

**SET MINIMUM GAS PRICE**

```
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.000000001ulava\"/" $HOME/.lava/config/app.toml
```

**ENABLE PROMETHEUS**

```
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.lava/config/config.toml
```

**RESET CHAIN DATA**

```
lavad tendermint unsafe-reset-all --keep-addr-book --home $HOME/.lava --keep-addr-book
```

**REMOVE NODE**

Please, before proceeding with the next step! All chain data will be lost! Make sure you have backed up your **priv\_validator\_key.json**!

```
cd $HOME
sudo systemctl stop lavad
sudo systemctl disable lavad
sudo rm /etc/systemd/system/lava.service
sudo systemctl daemon-reload
rm -f $(which lavad)
rm -rf $HOME/.lava
rm -rf $HOME/lava
```

### ⚙️ Service Management <a href="#service-management" id="service-management"></a>

**RELOAD SERVICE CONFIGURATION**

```
sudo systemctl daemon-reload
```

**ENABLE SERVICE**

```
sudo systemctl enable lavad
```

**DISABLE SERVICE**

```
sudo systemctl disable lavad
```

**START SERVICE**

```
sudo systemctl start lavad
```

**STOP SERVICE**

```
sudo systemctl stop lavad
```

**RESTART SERVICE**

```
sudo systemctl restart lavad
```

**CHECK SERVICE STATUS**

```
sudo systemctl status lavad
```

**CHECK SERVICE LOGS**

```
sudo journalctl -u lavad -f --no-hostname -o cat
```
