# Useful commands

### 🔑 Key management <a href="#key-management" id="key-management"></a>

**ADD NEW KEY**

```
artelad keys add wallet
```

**RECOVER EXISTING KEY**

```
artelad keys add wallet --recover
```

**LIST ALL KEYS**

```
artelad keys list
```

**DELETE KEY**

```
artelad keys delete wallet
```

**EXPORT KEY TO A FILE**

```
artelad keys export wallet
```

**IMPORT KEY FROM A FILE**

```
artelad keys import wallet wallet.backup
```

**QUERY WALLET BALANCE**

```
artelad q bank balances $(artelad keys show wallet -a)
```

### 👷 Validator management <a href="#validator-management" id="validator-management"></a>

Please make sure you have adjusted **moniker**, **identity**, **details** and **website** to match your values.

```
artelad tx staking create-validator \
--amount 1000000uart \
--from wallet \
--commission-rate 0.1 \
--commission-max-rate 0.2 \
--commission-max-change-rate 0.01 \
--min-self-delegation 1 \
--pubkey $(artelad tendermint show-validator) \
--moniker "MonikerName" \
--identity "" \
--website "" \
--details "CoinHunters Community" \
--chain-id artela_11822-1 \
--gas auto --gas-adjustment 1.5 \
-y
```

**UNJAIL VALIDATOR**

```
artelad tx slashing unjail --from wallet --chain-id artela_11822-1 --gas-adjustment 1.5 --gas auto --gas-prices 0.025art -y
```

**JAIL REASON**

```
artelad query slashing signing-info $(artelad tendermint show-validator)
```

**LIST ALL ACTIVE VALIDATORS**

```
artelad q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

**LIST ALL INACTIVE VALIDATORS**

```
artelad q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_UNBONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

**VIEW VALIDATOR DETAILS**

```
artelad q staking validator $(artelad keys show wallet --bech val -a)
```

### 💲 Token management <a href="#token-management" id="token-management"></a>

**WITHDRAW REWARDS FROM ALL VALIDATORS**

```
artelad tx distribution withdraw-rewards $(artelad keys show wallet --bech val -a) --commission --from wallet --chain-id artela_11822-1 --gas-adjustment 1.5 --gas auto --gas-prices 0.025art -y
```

**WITHDRAW COMMISSION AND REWARDS FROM YOUR VALIDATOR**

```
artelad tx distribution withdraw-rewards $(artelad keys show wallet --bech val -a) --commission --from wallet --chain-id artela_11822-1 --gas-adjustment 1.5 --gas auto --gas-prices 0.025art -y
```

**DELEGATE TOKENS TO YOURSELF**

```
artelad tx staking delegate $(artelad keys show wallet --bech val -a) 1000000uart --from wallet --chain-id artela_11822-1 --gas-adjustment 1.5 --gas auto --gas-prices 0.025art -y
```

**DELEGATE TOKENS TO VALIDATOR**

```
artelad tx staking delegate <TO_VALOPER_ADDRESS> 1000000uart --from wallet --chain-id artela_11822-1 --gas-adjustment 1.5 --gas auto --gas-prices 0.025art -y
```

**REDELEGATE TOKENS TO ANOTHER VALIDATOR**

```
artelad tx staking redelegate $(artelad keys show wallet --bech val -a) <TO_VALOPER_ADDRESS> 1000000uart --from wallet --chain-id artela_11822-1 --gas-adjustment 1.5 --gas auto --gas-prices 0.025art -y
```

**UNBOND TOKENS FROM YOUR VALIDATOR**

```
artelad tx staking unbond $(artelad keys show wallet --bech val -a) 1000000uart --from wallet --chain-id artela_11822-1 --gas-adjustment 1.5 --gas auto --gas-prices 0.025art -y
```

**SEND TOKENS TO THE WALLET**

```
artelad tx bank send wallet <TO_WALLET_ADDRESS> 1000000uart --from wallet --chain-id artela_11822-1 --gas-adjustment 1.5 --gas auto --gas-prices 0.025art -y
```

### 🗳 Governance <a href="#governance" id="governance"></a>

**LIST ALL PROPOSALS**

```
artelad query gov proposals
```

**VIEW PROPOSAL BY ID**

```
artelad query gov proposal 1
```

**VOTE ‘YES’**

```
artelad tx gov vote 1 yes --from wallet --chain-id artela_11822-1 --gas-adjustment 1.5 --gas auto --gas-prices 0.025art -y
```

**VOTE ‘NO’**

```
artelad tx gov vote 1 no --from wallet --chain-id artela_11822-1 --gas-adjustment 1.5 --gas auto --gas-prices 0.025art -y
```

**VOTE ‘ABSTAIN’**

```
artelad tx gov vote 1 abstain --from wallet --chain-id artela_11822-1 --gas-adjustment 1.5 --gas auto --gas-prices 0.025art -y
```

**VOTE ‘NOWITHVETO’**

```
artelad tx gov vote 1 NoWithVeto --from wallet --chain-id artela_11822-1 --gas-adjustment 1.5 --gas auto --gas-prices 0.025art -y
```

### ⚡️ Utility <a href="#utility" id="utility"></a>

**UPDATE PORTS**

```
UPDATE PORTS
CUSTOM_PORT=110
sed -i -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${CUSTOM_PORT}58\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${CUSTOM_PORT}57\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${CUSTOM_PORT}60\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${CUSTOM_PORT}56\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${CUSTOM_PORT}66\"%" $HOME/.artelad/config/config.toml
sed -i -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:${CUSTOM_PORT}17\"%; s%^address = \":8080\"%address = \":${CUSTOM_PORT}80\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${CUSTOM_PORT}90\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${CUSTOM_PORT}91\"%" $HOME/.artelad/config/app.toml
```

**UPDATE INDEXER**

**Disable indexer**

```
sed -i -e 's|^indexer *=.*|indexer = "null"|' $HOME/.artelad/config/config.toml
```

**Enable indexer**

```
sed -i -e 's|^indexer *=.*|indexer = "kv"|' $HOME/.artelad/config/config.toml
```

**UPDATE PRUNING**

```
sed -i \
  -e 's|^pruning *=.*|pruning = "custom"|' \
  -e 's|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|' \
  -e 's|^pruning-keep-every *=.*|pruning-keep-every = "0"|' \
  -e 's|^pruning-interval *=.*|pruning-interval = "19"|' \
  $HOME/.artelad/config/app.toml
```

### 🚨 Maintenance <a href="#maintenance" id="maintenance"></a>

**GET VALIDATOR INFO**

```
artelad status 2>&1 | jq .ValidatorInfo
```

**GET SYNC INFO**

```
artelad status 2>&1 | jq
```

**GET NODE PEER**

```
echo $(artelad tendermint show-node-id)'@'$(curl -s ifconfig.me)':'$(cat $HOME/.artela_11822-1/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```

**CHECK IF VALIDATOR KEY IS CORRECT**

```
[[ $(artelad q staking validator $(artelad keys show wallet --bech val -a) -oj | jq -r .consensus_pubkey.key) = $(artelad status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

**GET LIVE PEERS**

```
curl -sS http://localhost:26657/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}'
```

**SET MINIMUM GAS PRICE**

```
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.025art\"/" $HOME/.artelad/config/app.toml
```

**ENABLE PROMETHEUS**

```
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.artelad/config/config.toml
```

**RESET CHAIN DATA**

```
artelad tendermint unsafe-reset-all --keep-addr-book --home $HOME/.artelad --keep-addr-book
```

**REMOVE NODE**

Please, before proceeding with the next step! All chain data will be lost! Make sure you have backed up your **priv\_validator\_key.json**!

```
cd $HOME
sudo systemctl stop artelad
sudo systemctl disable artelad
sudo rm /etc/systemd/system/artelad.service
sudo systemctl daemon-reload
rm -f $(which artelad)
rm -rf $HOME/.artelad
rm -rf $HOME/artela
```

### ⚙️ Service Management <a href="#service-management" id="service-management"></a>

**RELOAD SERVICE CONFIGURATION**

```
sudo systemctl daemon-reload
```

**ENABLE SERVICE**

```
sudo systemctl enable artelad
```

**DISABLE SERVICE**

```
sudo systemctl disable artelad
```

**START SERVICE**

```
sudo systemctl start artelad
```

**STOP SERVICE**

```
sudo systemctl stop artelad
```

**RESTART SERVICE**

```
sudo systemctl restart artelad
```

**CHECK SERVICE STATUS**

```
sudo systemctl status artelad
```

**CHECK SERVICE LOGS**

```
sudo journalctl -u artelad -f --no-hostname -o cat
```
