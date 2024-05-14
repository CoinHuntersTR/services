# Useful commands

### 🔑 Key management <a href="#key-management" id="key-management"></a>

**ADD NEW KEY**

```
initiad keys add wallet
```

**RECOVER EXISTING KEY**

```
initiad keys add wallet --recover
```

**LIST ALL KEYS**

```
initiad keys list
```

**DELETE KEY**

```
initiad keys delete wallet
```

**EXPORT KEY TO A FILE**

```
initiad keys export wallet
```

**IMPORT KEY FROM A FILE**

```
initiad keys import wallet wallet.backup
```

**QUERY WALLET BALANCE**

```
initiad q bank balances $(initiad keys show wallet -a)
```

### 👷 Validator management <a href="#validator-management" id="validator-management"></a>

Please make sure you have adjusted **moniker**, **identity**, **details** and **website** to match your values.

**CREATE NEW VALIDATOR**

```
initiad tx mstaking create-validator \
--amount 1000000uinit \
--pubkey $(initiad tendermint show-validator) \
--moniker "YOUR_MONIKER_NAME" \
--identity "YOUR_KEYBASE_ID" \
--details "YOUR_DETAILS" \
--website "YOUR_WEBSITE_URL" \
--chain-id initiation-1 \
--commission-rate 0.05 \
--commission-max-rate 0.20 \
--commission-max-change-rate 0.05 \
--from wallet \
--gas-adjustment 1.4 \
--gas auto \
--gas-prices 0.15uinit \
-y
```

**EDIT EXISTING VALIDATOR**

```
initiad tx mstaking edit-validator \
--new-moniker "YOUR_MONIKER_NAME" \
--identity "YOUR_KEYBASE_ID" \
--details "YOUR_DETAILS" \
--website "YOUR_WEBSITE_URL" \
--chain-id initiation-1 \
--commission-rate 0.05 \
--from wallet \
--gas-adjustment 1.4 \
--gas auto \
--gas-prices 0.15uinit \
-y
```

**UNJAIL VALIDATOR**

```
initiad tx slashing unjail --from wallet --chain-id initiation-1 --gas-adjustment 1.4 --gas auto --gas-prices 0.15uinit -y
```

**JAIL REASON**

```
initiad query slashing signing-info $(initiad tendermint show-validator)
```

**LIST ALL ACTIVE VALIDATORS**

```
initiad q mstaking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

**LIST ALL INACTIVE VALIDATORS**

```
initiad q mstaking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_UNBONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

**VIEW VALIDATOR DETAILS**

```
initiad q mstaking validator $(initiad keys show wallet --bech val -a)
```

### 💲 Token management <a href="#token-management" id="token-management"></a>

**WITHDRAW REWARDS FROM ALL VALIDATORS**

```
initiad tx distribution withdraw-all-rewards --from wallet --chain-id initiation-1 --gas-adjustment 1.4 --gas auto --gas-prices 0.15uinit -y
```

**WITHDRAW COMMISSION AND REWARDS FROM YOUR VALIDATOR**

```
initiad tx distribution withdraw-rewards $(initiad keys show wallet --bech val -a) --commission --from wallet --chain-id initiation-1 --gas-adjustment 1.4 --gas auto --gas-prices 0.15uinit -y
```

**DELEGATE TOKENS TO YOURSELF**

```
initiad tx mstaking delegate $(initiad keys show wallet --bech val -a) 1000000uinit --from wallet --chain-id initiation-1 --gas-adjustment 1.4 --gas auto --gas-prices 0.15uinit -y
```

**DELEGATE TOKENS TO VALIDATOR**

```
initiad tx mstaking delegate <TO_VALOPER_ADDRESS> 1000000uinit --from wallet --chain-id initiation-1 --gas-adjustment 1.4 --gas auto --gas-prices 0.15uinit -y
```

**REDELEGATE TOKENS TO ANOTHER VALIDATOR**

```
initiad tx mstaking redelegate $(initiad keys show wallet --bech val -a) <TO_VALOPER_ADDRESS> 1000000uinit --from wallet --chain-id initiation-1 --gas-adjustment 1.4 --gas auto --gas-prices 0.15uinit -y
```

**UNBOND TOKENS FROM YOUR VALIDATOR**

```
initiad tx mstaking unbond $(initiad keys show wallet --bech val -a) 1000000uinit --from wallet --chain-id initiation-1 --gas-adjustment 1.4 --gas auto --gas-prices 0.15uinit -y
```

**SEND TOKENS TO THE WALLET**

```
initiad tx bank send wallet <TO_WALLET_ADDRESS> 1000000uinit --from wallet --chain-id initiation-1 --gas-adjustment 1.4 --gas auto --gas-prices 0.15uinit -y
```

### 🗳 Governance <a href="#governance" id="governance"></a>

**LIST ALL PROPOSALS**

```
initiad query gov proposals
```

**VIEW PROPOSAL BY ID**

```
initiad query gov proposal 1
```

**VOTE ‘YES’**

```
initiad tx gov vote 1 yes --from wallet --chain-id initiation-1 --gas-adjustment 1.4 --gas auto --gas-prices 0.15uinit -y
```

**VOTE ‘NO’**

```
initiad tx gov vote 1 no --from wallet --chain-id initiation-1 --gas-adjustment 1.4 --gas auto --gas-prices 0.15uinit -y
```

**VOTE ‘ABSTAIN’**

```
initiad tx gov vote 1 abstain --from wallet --chain-id initiation-1 --gas-adjustment 1.4 --gas auto --gas-prices 0.15uinit -y
```

**VOTE ‘NOWITHVETO’**

```
initiad tx gov vote 1 NoWithVeto --from wallet --chain-id initiation-1 --gas-adjustment 1.4 --gas auto --gas-prices 0.15uinit -y
```

### ⚡️ Utility <a href="#utility" id="utility"></a>

**UPDATE PORTS**

```
CUSTOM_PORT=110
sed -i -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${CUSTOM_PORT}58\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${CUSTOM_PORT}57\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${CUSTOM_PORT}60\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${CUSTOM_PORT}56\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${CUSTOM_PORT}66\"%" $HOME/.initia/config/config.toml
sed -i -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:${CUSTOM_PORT}17\"%; s%^address = \":8080\"%address = \":${CUSTOM_PORT}80\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${CUSTOM_PORT}90\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${CUSTOM_PORT}91\"%" $HOME/.initia/config/app.toml
```

**UPDATE INDEXER**

**Disable indexer**

```
sed -i -e 's|^indexer *=.*|indexer = "null"|' $HOME/.initia/config/config.toml
```

**Enable indexer**

```
sed -i -e 's|^indexer *=.*|indexer = "kv"|' $HOME/.initia/config/config.toml
```

**UPDATE PRUNING**

```
sed -i \
  -e 's|^pruning *=.*|pruning = "custom"|' \
  -e 's|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|' \
  -e 's|^pruning-keep-every *=.*|pruning-keep-every = "0"|' \
  -e 's|^pruning-interval *=.*|pruning-interval = "19"|' \
  $HOME/.initia/config/app.toml
```

### 🚨 Maintenance <a href="#maintenance" id="maintenance"></a>

**GET VALIDATOR INFO**

```
initiad status 2>&1 | jq .ValidatorInfo
```

**GET SYNC INFO**

```
initiad status 2>&1 | jq .SyncInfo
```

**GET NODE PEER**

```
echo $(initiad tendermint show-node-id)'@'$(curl -s ifconfig.me)':'$(cat $HOME/.initia/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```

**CHECK IF VALIDATOR KEY IS CORRECT**

```
[[ $(initiad q mstaking validator $(initiad keys show wallet --bech val -a) -oj | jq -r .consensus_pubkey.key) = $(initiad status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

**GET LIVE PEERS**

```
curl -sS http://localhost:17957/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}'
```

**SET MINIMUM GAS PRICE**

```
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.15uinit,0.01uusdc\"/" $HOME/.initia/config/app.toml
```

**ENABLE PROMETHEUS**

```
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.initia/config/config.toml
```

**RESET CHAIN DATA**

```
initiad tendermint unsafe-reset-all --keep-addr-book --home $HOME/.initia --keep-addr-book
```

**REMOVE NODE**

Please, before proceeding with the next step! All chain data will be lost! Make sure you have backed up your **priv\_validator\_key.json**!

```
cd $HOME
sudo systemctl stop initia.service
sudo systemctl disable initia.service
sudo rm /etc/systemd/system/initia.service
sudo systemctl daemon-reload
rm -f $(which initiad)
rm -rf $HOME/.initia
rm -rf $HOME/initia
```

### ⚙️ Service Management <a href="#service-management" id="service-management"></a>

**RELOAD SERVICE CONFIGURATION**

```
sudo systemctl daemon-reload
```

**ENABLE SERVICE**

```
sudo systemctl enable initia.service
```

**DISABLE SERVICE**

```
sudo systemctl disable initia.service
```

**START SERVICE**

```
sudo systemctl start initia.service
```

**STOP SERVICE**

```
sudo systemctl stop initia.service
```

**RESTART SERVICE**

```
sudo systemctl restart initia.service
```

**CHECK SERVICE STATUS**

```
sudo systemctl status initia.service
```

**CHECK SERVICE LOGS**

```
sudo journalctl -u initia.service -f --no-hostname -o cat
```
