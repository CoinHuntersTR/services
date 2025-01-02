# Useful commands

### 🔑 Key management <a href="#key-management" id="key-management"></a>

**ADD NEW KEY**

```
kopid keys add wallet
```

**RECOVER EXISTING KEY**

```
kopid keys add wallet --recover
```

**LIST ALL KEYS**

```
kopid keys list
```

**DELETE KEY**

```
kopid keys delete wallet
```

**EXPORT KEY TO A FILE**

```
kopid keys export wallet
```

**IMPORT KEY FROM A FILE**

```
kopid keys import wallet wallet.backup
```

**QUERY WALLET BALANCE**

```
kopid q bank balances $(kopid keys show wallet -a)
```

### 👷 Validator management <a href="#validator-management" id="validator-management"></a>

Please make sure you have adjusted **moniker**, **identity**, **details** and **website** to match your values.

**CREATE NEW VALIDATOR**

```
kopid tx staking create-validator \
--amount 1000000ukopi \
--from $WALLET \
--commission-rate 0.1 \
--commission-max-rate 0.2 \
--commission-max-change-rate 0.01 \
--min-self-delegation 1 \
--pubkey $(kopid tendermint show-validator) \
--moniker "test" \
--identity "" \
--website "" \
--details "I love blockchain ❤️" \
--chain-id luwak-1 \
--gas auto --gas-adjustment 1.5 --gas-prices  0ukopi \
-y
```

**EDIT EXISTING VALIDATOR**

```
kopid tx staking edit-validator \
--new-moniker "YOUR_MONIKER_NAME" \
--identity "YOUR_KEYBASE_ID" \
--details "YOUR_DETAILS" \
--website "YOUR_WEBSITE_URL" \
--chain-id luwak-1 \
--commission-rate 0.05 \
--from wallet \
--gas-adjustment 1.5 \
--gas auto \
--gas-prices  0ukopi \
-y
```

**UNJAIL VALIDATOR**

```
kopid tx slashing unjail --from wallet --chain-id luwak-1 --gas-adjustment 1.5 --gas auto --gas-prices  0ukopi -y
```

**JAIL REASON**

```
kopid query slashing signing-info $(kopid tendermint show-validator)
```

**LIST ALL ACTIVE VALIDATORS**

```
kopid q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

**LIST ALL INACTIVE VALIDATORS**

```
kopid q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_UNBONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

**VIEW VALIDATOR DETAILS**

```
kopid q staking validator $(kopid keys show wallet --bech val -a)
```

### 💲 Token management <a href="#token-management" id="token-management"></a>

**WITHDRAW REWARDS FROM ALL VALIDATORS**

```
kopid tx distribution withdraw-all-rewards --from wallet --chain-id luwak-1 --gas-adjustment 1.5 --gas auto --gas-prices  0ukopi -y
```

**WITHDRAW COMMISSION AND REWARDS FROM YOUR VALIDATOR**

```
kopid tx distribution withdraw-rewards $(kopid keys show wallet --bech val -a) --commission --from wallet --chain-id luwak-1 --gas-adjustment 1.5 --gas auto --gas-prices  0ukopi -y
```

**DELEGATE TOKENS TO YOURSELF**

```
kopid tx staking delegate $(kopid keys show wallet --bech val -a) 1000000ukopi --from wallet --chain-id luwak-1 --gas-adjustment 1.5 --gas auto --gas-prices  0ukopi -y
```

**DELEGATE TOKENS TO VALIDATOR**

```
kopid tx staking delegate <TO_VALOPER_ADDRESS> 1000000ukopi --from wallet --chain-id luwak-1 --gas-adjustment 1.5 --gas auto --gas-prices  0ukopi -y
```

**REDELEGATE TOKENS TO ANOTHER VALIDATOR**

```
kopid tx staking redelegate $(kopid keys show wallet --bech val -a) <TO_VALOPER_ADDRESS> 1000000ukopi --from wallet --chain-id luwak-1 --gas-adjustment 1.5 --gas auto --gas-prices  0ukopi -y
```

**UNBOND TOKENS FROM YOUR VALIDATOR**

```
kopid tx staking unbond $(kopid keys show wallet --bech val -a) 1000000ukopi --from wallet --chain-id luwak-1 --gas-adjustment 1.5 --gas auto --gas-prices  0ukopi -y
```

**SEND TOKENS TO THE WALLET**

```
kopid tx bank send wallet <TO_WALLET_ADDRESS> 1000000ukopi --from wallet --chain-id luwak-1 --gas-adjustment 1.5 --gas auto --gas-prices  0ukopi -y
```

### 🗳 Governance <a href="#governance" id="governance"></a>

**LIST ALL PROPOSALS**

```
kopid query gov proposals
```

**VIEW PROPOSAL BY ID**

```
kopid query gov proposal 1
```

**VOTE ‘YES’**

```
kopid tx gov vote 1 yes --from wallet --chain-id luwak-1 --gas-adjustment 1.5 --gas auto --gas-prices  0ukopi -y
```

**VOTE ‘NO’**

```
kopid tx gov vote 1 no --from wallet --chain-id luwak-1 --gas-adjustment 1.5 --gas auto --gas-prices  0ukopi -y
```

**VOTE ‘ABSTAIN’**

```
kopid tx gov vote 1 abstain --from wallet --chain-id luwak-1 --gas-adjustment 1.5 --gas auto --gas-prices  0ukopi -y
```

**VOTE ‘NOWITHVETO’**

```
kopid tx gov vote 1 NoWithVeto --from wallet --chain-id luwak-1 --gas-adjustment 1.5 --gas auto --gas-prices  0ukopi -y
```

### ⚡️ Utility <a href="#utility" id="utility"></a>

**UPDATE PORTS**

```
UPDATE PORTS
CUSTOM_PORT=110
sed -i -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${CUSTOM_PORT}58\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${CUSTOM_PORT}57\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${CUSTOM_PORT}60\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${CUSTOM_PORT}56\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${CUSTOM_PORT}66\"%" $HOME/.kopid/config/config.toml
sed -i -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:${CUSTOM_PORT}17\"%; s%^address = \":8080\"%address = \":${CUSTOM_PORT}80\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${CUSTOM_PORT}90\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${CUSTOM_PORT}91\"%" $HOME/.kopid/config/app.toml
```

**UPDATE INDEXER**

**Disable indexer**

```
sed -i -e 's|^indexer *=.*|indexer = "null"|' $HOME/.kopid/config/config.toml
```

**Enable indexer**

```
sed -i -e 's|^indexer *=.*|indexer = "kv"|' $HOME/.kopid/config/config.toml
```

**UPDATE PRUNING**

```
sed -i \
  -e 's|^pruning *=.*|pruning = "custom"|' \
  -e 's|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|' \
  -e 's|^pruning-keep-every *=.*|pruning-keep-every = "0"|' \
  -e 's|^pruning-interval *=.*|pruning-interval = "19"|' \
  $HOME/.kopid/config/app.toml
```

### 🚨 Maintenance <a href="#maintenance" id="maintenance"></a>

**GET VALIDATOR INFO**

```
kopid status 2>&1 | jq .ValidatorInfo
```

**GET SYNC INFO**

```
kopid status 2>&1 | jq
```

**GET NODE PEER**

```
echo $(kopid tendermint show-node-id)'@'$(curl -s ifconfig.me)':'$(cat $HOME/.kopid/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```

**CHECK IF VALIDATOR KEY IS CORRECT**

```
[[ $(kopid q staking validator $(kopid keys show wallet --bech val -a) -oj | jq -r .consensus_pubkey.key) = $(kopid status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

**GET LIVE PEERS**

```
curl -sS http://localhost:14657/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}'
```

**SET MINIMUM GAS PRICE**

```
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.01uom\"/" $HOME/.kopid/config/app.toml
```

**ENABLE PROMETHEUS**

```
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.kopid/config/config.toml
```

**RESET CHAIN DATA**

```
kopid tendermint unsafe-reset-all --keep-addr-book --home $HOME/.kopid --keep-addr-book
```

**REMOVE NODE**

Please, before proceeding with the next step! All chain data will be lost! Make sure you have backed up your **priv\_validator\_key.json**!

```
cd $HOME
sudo systemctl stop kopid.service
sudo systemctl disable kopid.service
sudo rm /etc/systemd/system/kopid.service
sudo systemctl daemon-reload
rm -f $(which kopid)
rm -rf $HOME/.kopid
```

### ⚙️ Service Management <a href="#service-management" id="service-management"></a>

**RELOAD SERVICE CONFIGURATION**

```
sudo systemctl daemon-reload
```

**ENABLE SERVICE**

```
sudo systemctl enable kopid.service
```

**DISABLE SERVICE**

```
sudo systemctl disable kopid.service
```

**START SERVICE**

```
sudo systemctl start kopid.service
```

**STOP SERVICE**

```
sudo systemctl stop kopid.service
```

**RESTART SERVICE**

```
sudo systemctl restart kopid.service
```

**CHECK SERVICE STATUS**

```
sudo systemctl status kopid.service
```

**CHECK SERVICE LOGS**

```
sudo journalctl -u kopid.service -f --no-hostname -o cat
```
