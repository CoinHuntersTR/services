# Useful commands

### 🔑 Key management <a href="#key-management" id="key-management"></a>

**ADD NEW KEY**

```
nibid keys add wallet
```

**RECOVER EXISTING KEY**

```
nibid keys add wallet --recover
```

**LIST ALL KEYS**

```
nibid keys list
```

**DELETE KEY**

```
nibid keys delete wallet
```

**EXPORT KEY TO A FILE**

```
nibid keys export wallet
```

**IMPORT KEY FROM A FILE**

```
nibid keys import wallet wallet.backup
```

**QUERY WALLET BALANCE**

```
nibid q bank balances $(nibid keys show wallet -a)
```

### 👷 Validator management <a href="#validator-management" id="validator-management"></a>

Please make sure you have adjusted **moniker**, **identity**, **details** and **website** to match your values.

**CREATE NEW VALIDATOR**

```
nibid tx staking create-validator \
--amount 1000000unibi \
--pubkey $(nibid tendermint show-validator) \
--moniker "YOUR_MONIKER_NAME" \
--identity "YOUR_KEYBASE_ID" \
--details "YOUR_DETAILS" \
--website "YOUR_WEBSITE_URL" \
--chain-id cataclysm-1 \
--commission-rate 0.05 \
--commission-max-rate 0.20 \
--commission-max-change-rate 0.05 \
--min-self-delegation 1 \
--from wallet \
--fees 5000unibi \
-y
```

**EDIT EXISTING VALIDATOR**

```
nibid tx staking edit-validator \
--new-moniker "YOUR_MONIKER_NAME" \
--identity "YOUR_KEYBASE_ID" \
--details "YOUR_DETAILS" \
--website "YOUR_WEBSITE_URL" \
--chain-id cataclysm-1 \
--commission-rate 0.05 \
--from wallet \
--fees 5000unibi \
-y
```

**UNJAIL VALIDATOR**

```
nibid tx slashing unjail --from wallet --chain-id cataclysm-1 --fees 5000unibi -y
```

**JAIL REASON**

```
nibid query slashing signing-info $(nibid tendermint show-validator)
```

**LIST ALL ACTIVE VALIDATORS**

```
nibid q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

**LIST ALL INACTIVE VALIDATORS**

```
nibid q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_UNBONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

**VIEW VALIDATOR DETAILS**

```
nibid q staking validator $(nibid keys show wallet --bech val -a)
```

### 💲 Token management <a href="#token-management" id="token-management"></a>

**WITHDRAW REWARDS FROM ALL VALIDATORS**

```
nibid tx distribution withdraw-all-rewards --from wallet --chain-id cataclysm-1 --fees 5000unibi -y
```

**WITHDRAW COMMISSION AND REWARDS FROM YOUR VALIDATOR**

```
nibid tx distribution withdraw-rewards $(nibid keys show wallet --bech val -a) --commission --from wallet --chain-id cataclysm-1 --fees 5000unibi -y
```

**DELEGATE TOKENS TO YOURSELF**

```
nibid tx staking delegate $(nibid keys show wallet --bech val -a) 1000000unibi --from wallet --chain-id cataclysm-1 --fees 5000unibi -y
```

**DELEGATE TOKENS TO VALIDATOR**

```
nibid tx staking delegate <TO_VALOPER_ADDRESS> 1000000unibi --from wallet --chain-id cataclysm-1 --fees 5000unibi -y
```

**REDELEGATE TOKENS TO ANOTHER VALIDATOR**

```
nibid tx staking redelegate $(nibid keys show wallet --bech val -a) <TO_VALOPER_ADDRESS> 1000000unibi --from wallet --chain-id cataclysm-1 --fees 5000unibi -y
```

**UNBOND TOKENS FROM YOUR VALIDATOR**

```
nibid tx staking unbond $(nibid keys show wallet --bech val -a) 1000000unibi --from wallet --chain-id cataclysm-1 --fees 5000unibi -y
```

**SEND TOKENS TO THE WALLET**

```
nibid tx bank send wallet <TO_WALLET_ADDRESS> 1000000unibi --from wallet --chain-id cataclysm-1 --fees 5000unibi -y
```

### 🗳 Governance <a href="#governance" id="governance"></a>

**LIST ALL PROPOSALS**

```
nibid query gov proposals
```

**VIEW PROPOSAL BY ID**

```
nibid query gov proposal 1
```

**VOTE ‘YES’**

```
nibid tx gov vote 1 yes --from wallet --chain-id cataclysm-1 --fees 5000unibi -y
```

**VOTE ‘NO’**

```
nibid tx gov vote 1 no --from wallet --chain-id cataclysm-1 --fees 5000unibi -y
```

**VOTE ‘ABSTAIN’**

```
nibid tx gov vote 1 abstain --from wallet --chain-id cataclysm-1 --fees 5000unibi -y
```

**VOTE ‘NOWITHVETO’**

```
nibid tx gov vote 1 NoWithVeto --from wallet --chain-id cataclysm-1 --fees 5000unibi -y
```

### ⚡️ Utility <a href="#utility" id="utility"></a>

**UPDATE PORTS**

```
CUSTOM_PORT=110
sed -i -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${CUSTOM_PORT}58\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${CUSTOM_PORT}57\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${CUSTOM_PORT}60\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${CUSTOM_PORT}56\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${CUSTOM_PORT}66\"%" $HOME/.nibid/config/config.toml
sed -i -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:${CUSTOM_PORT}17\"%; s%^address = \":8080\"%address = \":${CUSTOM_PORT}80\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${CUSTOM_PORT}90\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${CUSTOM_PORT}91\"%" $HOME/.nibid/config/app.toml
```

**UPDATE INDEXER**

**Disable indexer**

```
sed -i -e 's|^indexer *=.*|indexer = "null"|' $HOME/.nibid/config/config.toml
```

**Enable indexer**

```
sed -i -e 's|^indexer *=.*|indexer = "kv"|' $HOME/.nibid/config/config.toml
```

**UPDATE PRUNING**

```
sed -i \
  -e 's|^pruning *=.*|pruning = "custom"|' \
  -e 's|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|' \
  -e 's|^pruning-keep-every *=.*|pruning-keep-every = "0"|' \
  -e 's|^pruning-interval *=.*|pruning-interval = "19"|' \
  $HOME/.nibid/config/app.toml
```

### 🚨 Maintenance <a href="#maintenance" id="maintenance"></a>

**GET VALIDATOR INFO**

```
nibid status 2>&1 | jq .ValidatorInfo
```

**GET SYNC INFO**

```
nibid status 2>&1 | jq .SyncInfo
```

**GET NODE PEER**

```
echo $(nibid tendermint show-node-id)'@'$(curl -s ifconfig.me)':'$(cat $HOME/.nibid/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```

**CHECK IF VALIDATOR KEY IS CORRECT**

```
[[ $(nibid q staking validator $(nibid keys show wallet --bech val -a) -oj | jq -r .consensus_pubkey.key) = $(nibid status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

**GET LIVE PEERS**

```
curl -sS http://localhost:13957/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}'
```

**SET MINIMUM GAS PRICE**

```
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.025unibi\"/" $HOME/.nibid/config/app.toml
```

**ENABLE PROMETHEUS**

```
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.nibid/config/config.toml
```

**RESET CHAIN DATA**

```
nibid tendermint unsafe-reset-all --keep-addr-book --home $HOME/.nibid --keep-addr-book
```

**REMOVE NODE**

Please, before proceeding with the next step! All chain data will be lost! Make sure you have backed up your **priv\_validator\_key.json**!

```
cd $HOME
sudo systemctl stop nibiru.service
sudo systemctl disable nibiru.service
sudo rm /etc/systemd/system/nibiru.service
sudo systemctl daemon-reload
rm -f $(which nibid)
rm -rf $HOME/.nibid
rm -rf $HOME/nibiru
```

### ⚙️ Service Management <a href="#service-management" id="service-management"></a>

**RELOAD SERVICE CONFIGURATION**

```
sudo systemctl daemon-reload
```

**ENABLE SERVICE**

```
sudo systemctl enable nibiru.service
```

**DISABLE SERVICE**

```
sudo systemctl disable nibiru.service
```

**START SERVICE**

```
sudo systemctl start nibiru.service
```

**STOP SERVICE**

```
sudo systemctl stop nibiru.service
```

**RESTART SERVICE**

```
sudo systemctl restart nibiru.service
```

**CHECK SERVICE STATUS**

```
sudo systemctl status nibiru.service
```

**CHECK SERVICE LOGS**

```
sudo journalctl -u nibiru.service -f --no-hostname -o cat
```
