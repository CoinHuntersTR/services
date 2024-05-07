# Useful commands

### 🔑 Key management <a href="#key-management" id="key-management"></a>

**ADD NEW KEY**

```
zetacored keys add wallet
```

**RECOVER EXISTING KEY**

```
zetacored keys add wallet --recover
```

**LIST ALL KEYS**

```
zetacored keys list
```

**DELETE KEY**

```
zetacored keys delete wallet
```

**EXPORT KEY TO A FILE**

```
zetacored keys export wallet
```

**IMPORT KEY FROM A FILE**

```
zetacored keys import wallet wallet.backup
```

**QUERY WALLET BALANCE**

```
zetacored q bank balances $(zetacored keys show wallet -a)
```

### 👷 Validator management <a href="#validator-management" id="validator-management"></a>

Please make sure you have adjusted **moniker**, **identity**, **details** and **website** to match your values.

**CREATE NEW VALIDATOR**

```
zetacored tx staking create-validator \
--amount 1000000azeta \
--from $WALLET \
--commission-rate 0.1 \
--commission-max-rate 0.2 \
--commission-max-change-rate 0.01 \
--min-self-delegation 1 \
--pubkey $(zetacored tendermint show-validator) \
--moniker "test" \
--identity "" \
--website "" \
--details "I love blockchain ❤️" \
--chain-id zetachain_7000-1 \
--gas auto --gas-adjustment 1.5 --gas-prices 0.1azeta \
-y
```

**EDIT EXISTING VALIDATOR**

```
zetacored tx staking edit-validator \
--new-moniker "YOUR_MONIKER_NAME" \
--identity "YOUR_KEYBASE_ID" \
--details "YOUR_DETAILS" \
--website "YOUR_WEBSITE_URL" \
--chain-id zetachain_7000-1 \
--commission-rate 0.05 \
--from wallet \
--gas-adjustment 1.5 \
--gas auto \
--gas-prices 0.1azeta \
-y
```

**UNJAIL VALIDATOR**

```
zetacored tx slashing unjail --from wallet --chain-id zetachain_7000-1 --gas-adjustment 1.5 --gas auto --gas-prices 0.1azeta -y
```

**JAIL REASON**

```
zetacored query slashing signing-info $(zetacored tendermint show-validator)
```

**LIST ALL ACTIVE VALIDATORS**

```
zetacored q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

**LIST ALL INACTIVE VALIDATORS**

```
zetacored q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_UNBONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

**VIEW VALIDATOR DETAILS**

```
zetacored q staking validator $(zetacored keys show wallet --bech val -a)
```

### 💲 Token management <a href="#token-management" id="token-management"></a>

**WITHDRAW REWARDS FROM ALL VALIDATORS**

```
zetacored tx distribution withdraw-all-rewards --from wallet --chain-id zetachain_7000-1 --gas-adjustment 1.5 --gas auto --gas-prices 0.1azeta -y
```

**WITHDRAW COMMISSION AND REWARDS FROM YOUR VALIDATOR**

```
zetacored tx distribution withdraw-rewards $(zetacored keys show wallet --bech val -a) --commission --from wallet --chain-id zetachain_7000-1 --gas-adjustment 1.5 --gas auto --gas-prices 0.1azeta -y
```

**DELEGATE TOKENS TO YOURSELF**

```
zetacored tx staking delegate $(zetacored keys show wallet --bech val -a) 1000000azeta --from wallet --chain-id zetachain_7000-1 --gas-adjustment 1.5 --gas auto --gas-prices 0.1azeta -y
```

**DELEGATE TOKENS TO VALIDATOR**

```
zetacored tx staking delegate <TO_VALOPER_ADDRESS> 1000000azeta --from wallet --chain-id zetachain_7000-1 --gas-adjustment 1.5 --gas auto --gas-prices 0.1azeta -y
```

**REDELEGATE TOKENS TO ANOTHER VALIDATOR**

```
zetacored tx staking redelegate $(zetacored keys show wallet --bech val -a) <TO_VALOPER_ADDRESS> 1000000azeta --from wallet --chain-id zetachain_7000-1 --gas-adjustment 1.5 --gas auto --gas-prices 0.1azeta -y
```

**UNBOND TOKENS FROM YOUR VALIDATOR**

```
zetacored tx staking unbond $(zetacored keys show wallet --bech val -a) 1000000azeta --from wallet --chain-id zetachain_7000-1 --gas-adjustment 1.5 --gas auto --gas-prices 0.1azeta -y
```

**SEND TOKENS TO THE WALLET**

```
zetacored tx bank send wallet <TO_WALLET_ADDRESS> 1000000azeta --from wallet --chain-id zetachain_7000-1 --gas-adjustment 1.5 --gas auto --gas-prices 0.1azeta -y
```

### 🗳 Governance <a href="#governance" id="governance"></a>

**LIST ALL PROPOSALS**

```
zetacored query gov proposals
```

**VIEW PROPOSAL BY ID**

```
zetacored query gov proposal 1
```

**VOTE ‘YES’**

```
zetacored tx gov vote 1 yes --from wallet --chain-id zetachain_7000-1 --gas-adjustment 1.5 --gas auto --gas-prices 0.1azeta -y
```

**VOTE ‘NO’**

```
zetacored tx gov vote 1 no --from wallet --chain-id zetachain_7000-1 --gas-adjustment 1.5 --gas auto --gas-prices 0.1azeta -y
```

**VOTE ‘ABSTAIN’**

```
zetacored tx gov vote 1 abstain --from wallet --chain-id zetachain_7000-1 --gas-adjustment 1.5 --gas auto --gas-prices 0.1azeta -y
```

**VOTE ‘NOWITHVETO’**

```
zetacored tx gov vote 1 NoWithVeto --from wallet --chain-id zetachain_7000-1 --gas-adjustment 1.5 --gas auto --gas-prices 0.1azeta -y
```

### ⚡️ Utility <a href="#utility" id="utility"></a>

**UPDATE PORTS**

```
UPDATE PORTS
CUSTOM_PORT=110
sed -i -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${CUSTOM_PORT}58\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${CUSTOM_PORT}57\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${CUSTOM_PORT}60\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${CUSTOM_PORT}56\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${CUSTOM_PORT}66\"%" $HOME/.zetacored/config/config.toml
sed -i -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:${CUSTOM_PORT}17\"%; s%^address = \":8080\"%address = \":${CUSTOM_PORT}80\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${CUSTOM_PORT}90\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${CUSTOM_PORT}91\"%" $HOME/.zetacored/config/app.toml
```

**UPDATE INDEXER**

**Disable indexer**

```
sed -i -e 's|^indexer *=.*|indexer = "null"|' $HOME/.zetacored/config/config.toml
```

**Enable indexer**

```
sed -i -e 's|^indexer *=.*|indexer = "kv"|' $HOME/.zetacored/config/config.toml
```

**UPDATE PRUNING**

```
sed -i \
  -e 's|^pruning *=.*|pruning = "custom"|' \
  -e 's|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|' \
  -e 's|^pruning-keep-every *=.*|pruning-keep-every = "0"|' \
  -e 's|^pruning-interval *=.*|pruning-interval = "19"|' \
  $HOME/.zetacored/config/app.toml
```

### 🚨 Maintenance <a href="#maintenance" id="maintenance"></a>

**GET VALIDATOR INFO**

```
zetacored status 2>&1 | jq .ValidatorInfo
```

**GET SYNC INFO**

```
zetacored status 2>&1 | jq .SyncInfo
```

**GET NODE PEER**

```
echo $(zetacored tendermint show-node-id)'@'$(curl -s ifconfig.me)':'$(cat $HOME/.zetacored/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```

**CHECK IF VALIDATOR KEY IS CORRECT**

```
[[ $(zetacored q staking validator $(zetacored keys show wallet --bech val -a) -oj | jq -r .consensus_pubkey.key) = $(zetacored status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

**GET LIVE PEERS**

```
curl -sS http://localhost:14657/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}'
```

**SET MINIMUM GAS PRICE**

```
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"10000000000000mpx\"/" $HOME/.zetacored/config/app.toml
```

**ENABLE PROMETHEUS**

```
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.zetacored/config/config.toml
```

**RESET CHAIN DATA**

```
zetacored tendermint unsafe-reset-all --keep-addr-book --home $HOME/.zetacored --keep-addr-book
```

**REMOVE NODE**

Please, before proceeding with the next step! All chain data will be lost! Make sure you have backed up your **priv\_validator\_key.json**!

```
cd $HOME
sudo systemctl stop zetacored
sudo systemctl disable zetacored
sudo rm /etc/systemd/system/zetacored
sudo systemctl daemon-reload
rm -f $(which zetacored)
rm -rf $HOME/.zetacored
rm -rf $HOME/zetacored
```

### ⚙️ Service Management <a href="#service-management" id="service-management"></a>

**RELOAD SERVICE CONFIGURATION**

```
sudo systemctl daemon-reload
```

**ENABLE SERVICE**

```
sudo systemctl enable zetacored
```

**DISABLE SERVICE**

```
sudo systemctl disable zetacored
```

**START SERVICE**

```
sudo systemctl start zetacored
```

**STOP SERVICE**

```
sudo systemctl stop zetacored
```

**RESTART SERVICE**

```
sudo systemctl restart zetacored
```

**CHECK SERVICE STATUS**

```
sudo systemctl status zetacored
```

**CHECK SERVICE LOGS**

```
sudo journalctl -u zetacored -f --no-hostname -o cat
```
