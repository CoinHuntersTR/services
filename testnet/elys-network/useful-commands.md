# Useful commands

### 🔑 Key management <a href="#key-management" id="key-management"></a>

**ADD NEW KEY**

```
elysd keys add wallet
```

**RECOVER EXISTING KEY**

```
elysd keys add wallet --recover
```

**LIST ALL KEYS**

```
elysd keys list
```

**DELETE KEY**

```
elysd keys delete wallet
```

**EXPORT KEY TO A FILE**

```
elysd keys export wallet
```

**IMPORT KEY FROM A FILE**

```
elysd keys import wallet wallet.backup
```

**QUERY WALLET BALANCE**

```
elysd q bank balances $(elysd keys show wallet -a)
```

### 👷 Validator management <a href="#validator-management" id="validator-management"></a>

Please make sure you have adjusted **moniker**, **identity**, **details** and **website** to match your values.

```
elysd tx staking create-validator \
--amount 1000000uelys \
--from WALLET \
--commission-rate 0.1 \
--commission-max-rate 0.2 \
--commission-max-change-rate 0.01 \
--min-self-delegation 1 \
--pubkey $(elysd tendermint show-validator) \
--moniker "test" \
--identity "" \
--website "" \
--details "CoinHunters Community" \
--chain-id elystestnet-1 \
--gas auto --gas-adjustment 1.5 --fees 100uelys \
-y

**UNJAIL VALIDATOR**

```
elysd tx slashing unjail --from wallet --chain-id elystestnet-1 --gas-adjustment 1.5 --gas auto --gas-prices 100uelys -y
```

**JAIL REASON**

```
elysd query slashing signing-info $(elysd tendermint show-validator)
```

**LIST ALL ACTIVE VALIDATORS**

```
elysd q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

**LIST ALL INACTIVE VALIDATORS**

```
elysd q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_UNBONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

**VIEW VALIDATOR DETAILS**

```
elysd q staking validator $(elysd keys show wallet --bech val -a)
```

### 💲 Token management <a href="#token-management" id="token-management"></a>

**WITHDRAW REWARDS FROM ALL VALIDATORS**

```
elysd tx distribution withdraw-rewards $(elysd keys show wallet --bech val -a) --commission --from wallet --chain-id elystestnet-1 --gas-adjustment 1.5 --gas auto --gas-prices 100uelys -y
```

**WITHDRAW COMMISSION AND REWARDS FROM YOUR VALIDATOR**

```
elysd tx distribution withdraw-rewards $(elysd keys show wallet --bech val -a) --commission --from wallet --chain-id elystestnet-1 --gas-adjustment 1.5 --gas auto --gas-prices 100uelys -y
```

**DELEGATE TOKENS TO YOURSELF**

```
elysd tx staking delegate $(elysd keys show wallet --bech val -a) 1000000uelys --from wallet --chain-id elystestnet-1 --gas-adjustment 1.5 --gas auto --gas-prices 100uelys -y
```

**DELEGATE TOKENS TO VALIDATOR**

```
elysd tx staking delegate <TO_VALOPER_ADDRESS> 1000000uelys --from wallet --chain-id elystestnet-1 --gas-adjustment 1.5 --gas auto --gas-prices 100uelys -y
```

**REDELEGATE TOKENS TO ANOTHER VALIDATOR**

```
elysd tx staking redelegate $(elysd keys show wallet --bech val -a) <TO_VALOPER_ADDRESS> 1000000uelys --from wallet --chain-id elystestnet-1 --gas-adjustment 1.5 --gas auto --gas-prices 100uelys -y
```

**UNBOND TOKENS FROM YOUR VALIDATOR**

```
elysd tx staking unbond $(elysd keys show wallet --bech val -a) 1000000uelys --from wallet --chain-id elystestnet-1 --gas-adjustment 1.5 --gas auto --gas-prices 100uelys -y
```

**SEND TOKENS TO THE WALLET**

```
elysd tx bank send wallet <TO_WALLET_ADDRESS> 1000000uelys --from wallet --chain-id elystestnet-1 --gas-adjustment 1.5 --gas auto --gas-prices 100uelys -y
```

### 🗳 Governance <a href="#governance" id="governance"></a>

**LIST ALL PROPOSALS**

```
elysd query gov proposals
```

**VIEW PROPOSAL BY ID**

```
elysd query gov proposal 1
```

**VOTE ‘YES’**

```
elysd tx gov vote 1 yes --from wallet --chain-id elystestnet-1 --gas-adjustment 1.5 --gas auto --gas-prices 100uelys -y
```

**VOTE ‘NO’**

```
elysd tx gov vote 1 no --from wallet --chain-id elystestnet-1 --gas-adjustment 1.5 --gas auto --gas-prices 100uelys -y
```

**VOTE ‘ABSTAIN’**

```
elysd tx gov vote 1 abstain --from wallet --chain-id elystestnet-1 --gas-adjustment 1.5 --gas auto --gas-prices 100uelys -y
```

**VOTE ‘NOWITHVETO’**

```
elysd tx gov vote 1 NoWithVeto --from wallet --chain-id elystestnet-1 --gas-adjustment 1.5 --gas auto --gas-prices 100uelys -y
```

### ⚡️ Utility <a href="#utility" id="utility"></a>

**UPDATE PORTS**

```
UPDATE PORTS
CUSTOM_PORT=110
sed -i -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${CUSTOM_PORT}58\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${CUSTOM_PORT}57\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${CUSTOM_PORT}60\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${CUSTOM_PORT}56\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${CUSTOM_PORT}66\"%" $HOME/.elys/config/config.toml
sed -i -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:${CUSTOM_PORT}17\"%; s%^address = \":8080\"%address = \":${CUSTOM_PORT}80\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${CUSTOM_PORT}90\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${CUSTOM_PORT}91\"%" $HOME/.elys/config/app.toml
```

**UPDATE INDEXER**

**Disable indexer**

```
sed -i -e 's|^indexer *=.*|indexer = "null"|' $HOME/.elys/config/config.toml
```

**Enable indexer**

```
sed -i -e 's|^indexer *=.*|indexer = "kv"|' $HOME/.elys/config/config.toml
```

**UPDATE PRUNING**

```
sed -i \
  -e 's|^pruning *=.*|pruning = "custom"|' \
  -e 's|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|' \
  -e 's|^pruning-keep-every *=.*|pruning-keep-every = "0"|' \
  -e 's|^pruning-interval *=.*|pruning-interval = "19"|' \
  $HOME/.elys/config/app.toml
```

### 🚨 Maintenance <a href="#maintenance" id="maintenance"></a>

**GET VALIDATOR INFO**

```
elysd status 2>&1 | jq .ValidatorInfo
```

**GET SYNC INFO**

```
elysd status 2>&1 | jq
```

**GET NODE PEER**

```
echo $(elysd tendermint show-node-id)'@'$(curl -s ifconfig.me)':'$(cat $HOME/.elys/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```

**CHECK IF VALIDATOR KEY IS CORRECT**

```
[[ $(elysd q staking validator $(elysd keys show wallet --bech val -a) -oj | jq -r .consensus_pubkey.key) = $(elysd status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

**GET LIVE PEERS**

```
curl -sS http://localhost:26657/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}'
```

**SET MINIMUM GAS PRICE**

```
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"100uelys\"/" $HOME/.elys/config/app.toml
```

**ENABLE PROMETHEUS**

```
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.elys/config/config.toml
```

**RESET CHAIN DATA**

```
elysd tendermint unsafe-reset-all --keep-addr-book --home $HOME/.elys --keep-addr-book
```

**REMOVE NODE**

Please, before proceeding with the next step! All chain data will be lost! Make sure you have backed up your **priv\_validator\_key.json**!

```
cd $HOME
sudo systemctl stop elysd
sudo systemctl disable elysd
sudo rm /etc/systemd/system/elysd.service
sudo systemctl daemon-reload
rm -f $(which elysd)
rm -rf $HOME/.elys
rm -rf $HOME/elys
```

### ⚙️ Service Management <a href="#service-management" id="service-management"></a>

**RELOAD SERVICE CONFIGURATION**

```
sudo systemctl daemon-reload
```

**ENABLE SERVICE**

```
sudo systemctl enable elysd
```

**DISABLE SERVICE**

```
sudo systemctl disable elysd
```

**START SERVICE**

```
sudo systemctl start elysd
```

**STOP SERVICE**

```
sudo systemctl stop elysd
```

**RESTART SERVICE**

```
sudo systemctl restart elysd
```

**CHECK SERVICE STATUS**

```
sudo systemctl status elysd
```

**CHECK SERVICE LOGS**

```
sudo journalctl -u elysd -f --no-hostname -o cat
```
