# Useful commands

### 🔑 Key management <a href="#key-management" id="key-management"></a>

**ADD NEW KEY**

```
evmosd keys add wallet
```

**RECOVER EXISTING KEY**

```
evmosd keys add wallet --recover
```

**LIST ALL KEYS**

```
evmosd keys list
```

**DELETE KEY**

```
evmosd keys delete wallet
```

**EXPORT KEY TO A FILE**

```
evmosd keys export wallet
```

**IMPORT KEY FROM A FILE**

```
evmosd keys import wallet wallet.backup
```

**QUERY WALLET BALANCE**

```
evmosd q bank balances $(evmosd keys show wallet -a)
```

### 👷 Validator management <a href="#validator-management" id="validator-management"></a>

Please make sure you have adjusted **moniker**, **identity**, **details** and **website** to match your values.

**CREATE NEW VALIDATOR**

```
evmosd tx staking create-validator \
--amount 1000000aevmos \
--pubkey $(evmosd tendermint show-validator) \
--moniker "YOUR_MONIKER_NAME" \
--identity "YOUR_KEYBASE_ID" \
--details "YOUR_DETAILS" \
--website "YOUR_WEBSITE_URL" \
--chain-id zgtendermint_9000-1 \
--commission-rate 0.05 \
--commission-max-rate 0.20 \
--commission-max-change-rate 0.05 \
--min-self-delegation 1 \
--from wallet \
--gas-adjustment 1.5 \
--gas auto \
--gas-prices 99999aevmos \
-y
```

**EDIT EXISTING VALIDATOR**

```
evmosd tx staking edit-validator \
--new-moniker "YOUR_MONIKER_NAME" \
--identity "YOUR_KEYBASE_ID" \
--details "YOUR_DETAILS" \
--website "YOUR_WEBSITE_URL" \
--chain-id zgtendermint_9000-1 \
--commission-rate 0.05 \
--from wallet \
--gas-adjustment 1.5 \
--gas auto \
--gas-prices 99999aevmos \
-y
```

**UNJAIL VALIDATOR**

```
evmosd tx slashing unjail --from wallet --chain-id zgtendermint_9000-1 --gas-adjustment 1.5 --gas auto --gas-prices 99999aevmos -y
```

**JAIL REASON**

```
evmosd query slashing signing-info $(evmosd tendermint show-validator)
```

**LIST ALL ACTIVE VALIDATORS**

```
evmosd q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

**LIST ALL INACTIVE VALIDATORS**

```
evmosd q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_UNBONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

**VIEW VALIDATOR DETAILS**

```
evmosd q staking validator $(evmosd keys show wallet --bech val -a)
```

### 💲 Token management <a href="#token-management" id="token-management"></a>

**WITHDRAW REWARDS FROM ALL VALIDATORS**

```
evmosd tx distribution withdraw-all-rewards --from wallet --chain-id zgtendermint_9000-1 --gas-adjustment 1.5 --gas auto --gas-prices 99999aevmos -y
```

**WITHDRAW COMMISSION AND REWARDS FROM YOUR VALIDATOR**

```
evmosd tx distribution withdraw-rewards $(evmosd keys show wallet --bech val -a) --commission --from wallet --chain-id zgtendermint_9000-1 --gas-adjustment 1.5 --gas auto --gas-prices 99999aevmos -y
```

**DELEGATE TOKENS TO YOURSELF**

```
evmosd tx staking delegate $(evmosd keys show wallet --bech val -a) 1000000aevmos --from wallet --chain-id zgtendermint_9000-1 --gas-adjustment 1.5 --gas auto --gas-prices 99999aevmos -y
```

**DELEGATE TOKENS TO VALIDATOR**

```
evmosd tx staking delegate <TO_VALOPER_ADDRESS> 1000000udym --from wallet --chain-id zgtendermint_9000-1 --gas-adjustment 1.5 --gas auto --gas-prices 99999aevmos -y
```

**REDELEGATE TOKENS TO ANOTHER VALIDATOR**

```
evmosd tx staking redelegate $(evmosd keys show wallet --bech val -a) <TO_VALOPER_ADDRESS> 1000000aevmos --from wallet --chain-id zgtendermint_9000-1 --gas-adjustment 1.5 --gas auto --gas-prices 99999aevmos -y
```

**UNBOND TOKENS FROM YOUR VALIDATOR**

```
evmosd tx staking unbond $(evmosd keys show wallet --bech val -a) 1000000aevmos --from wallet --chain-id zgtendermint_9000-1 --gas-adjustment 1.5 --gas auto --gas-prices 99999aevmos -y
```

**SEND TOKENS TO THE WALLET**

```
evmosd tx bank send wallet <TO_WALLET_ADDRESS> 1000000aevmos --from wallet --chain-id zgtendermint_9000-1 --gas-adjustment 1.5 --gas auto --gas-prices 99999aevmos -y
```

### 🗳 Governance <a href="#governance" id="governance"></a>

**LIST ALL PROPOSALS**

```
evmosd query gov proposals
```

**VIEW PROPOSAL BY ID**

```
evmosd query gov proposal 1
```

**VOTE ‘YES’**

```
evmosd tx gov vote 1 yes --from wallet --chain-id zgtendermint_9000-1 --gas-adjustment 1.5 --gas auto --gas-prices 99999aevmos -y
```

**VOTE ‘NO’**

```
evmosd tx gov vote 1 no --from wallet --chain-id zgtendermint_9000-1 --gas-adjustment 1.5 --gas auto --gas-prices 99999aevmos -y
```

**VOTE ‘ABSTAIN’**

```
evmosd tx gov vote 1 abstain --from wallet --chain-id zgtendermint_9000-1 --gas-adjustment 1.5 --gas auto --gas-prices 99999aevmos -y
```

**VOTE ‘NOWITHVETO’**

```
evmosd tx gov vote 1 NoWithVeto --from wallet --chain-id zgtendermint_9000-1 --gas-adjustment 1.5 --gas auto --gas-prices 99999aevmos -y
```

### ⚡️ Utility <a href="#utility" id="utility"></a>

**UPDATE PORTS**

```
CUSTOM_PORT=110
sed -i -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${CUSTOM_PORT}58\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${CUSTOM_PORT}57\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${CUSTOM_PORT}60\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${CUSTOM_PORT}56\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${CUSTOM_PORT}66\"%" $HOME/.evmosd/config/config.toml
sed -i -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:${CUSTOM_PORT}17\"%; s%^address = \":8080\"%address = \":${CUSTOM_PORT}80\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${CUSTOM_PORT}90\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${CUSTOM_PORT}91\"%" $HOME/.evmosd/config/app.toml
```

**UPDATE INDEXER**

**Disable indexer**

```
sed -i -e 's|^indexer *=.*|indexer = "null"|' $HOME/.evmosd/config/config.toml
```

**Enable indexer**

```
sed -i -e 's|^indexer *=.*|indexer = "kv"|' $HOME/.evmosd/config/config.toml
```

**UPDATE PRUNING**

```
sed -i \
  -e 's|^pruning *=.*|pruning = "custom"|' \
  -e 's|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|' \
  -e 's|^pruning-keep-every *=.*|pruning-keep-every = "0"|' \
  -e 's|^pruning-interval *=.*|pruning-interval = "19"|' \
  $HOME/.evmosd/config/app.toml
```

### 🚨 Maintenance <a href="#maintenance" id="maintenance"></a>

**GET VALIDATOR INFO**

```
evmosd status 2>&1 | jq .ValidatorInfo
```

**GET SYNC INFO**

```
evmosd status 2>&1 | jq .SyncInfo
```

**GET NODE PEER**

```
echo $(evmosd tendermint show-node-id)'@'$(curl -s ifconfig.me)':'$(cat $HOME/.evmosd/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```

**CHECK IF VALIDATOR KEY IS CORRECT**

```
[[ $(evmosd q staking validator $(evmosd keys show wallet --bech val -a) -oj | jq -r .consensus_pubkey.key) = $(evmosd status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

**GET LIVE PEERS**

```
curl -sS http://localhost:14657/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}'
```

**SET MINIMUM GAS PRICE**

```
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"20000000000udym\"/" $HOME/.evmosd/config/app.toml
```

**ENABLE PROMETHEUS**

```
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.evmosd/config/config.toml
```

**RESET CHAIN DATA**

```
evmosd tendermint unsafe-reset-all --keep-addr-book --home $HOME/.evmosd --keep-addr-book
```

**REMOVE NODE**

Please, before proceeding with the next step! All chain data will be lost! Make sure you have backed up your **priv\_validator\_key.json**!

```
cd $HOME
sudo systemctl stop evmosd
sudo systemctl disable evmosd
sudo rm /etc/systemd/system/evmosd
sudo systemctl daemon-reload
rm -f $(which evmosd)
rm -rf $HOME/.evmosd
rm -rf $HOME/evmosd
```

### ⚙️ Service Management <a href="#service-management" id="service-management"></a>

**RELOAD SERVICE CONFIGURATION**

```
sudo systemctl daemon-reload
```

**ENABLE SERVICE**

```
sudo systemctl enable evmosd
```

**DISABLE SERVICE**

```
sudo systemctl disable evmosd
```

**START SERVICE**

```
sudo systemctl start evmosd
```

**STOP SERVICE**

```
sudo systemctl stop evmosd
```

**RESTART SERVICE**

```
sudo systemctl restart evmosd
```

**CHECK SERVICE STATUS**

```
sudo systemctl status evmosd
```

**CHECK SERVICE LOGS**

```
sudo journalctl -u evmosd -f --no-hostname -o cat
```
