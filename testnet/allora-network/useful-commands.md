# Useful commands

### 🔑 Key management <a href="#key-management" id="key-management"></a>

**ADD NEW KEY**

```
allorad keys add wallet
```

**RECOVER EXISTING KEY**

```
allorad keys add wallet --recover
```

**LIST ALL KEYS**

```
allorad keys list
```

**DELETE KEY**

```
allorad keys delete wallet
```

**EXPORT KEY TO A FILE**

```
allorad keys export wallet
```

**IMPORT KEY FROM A FILE**

```
allorad keys import wallet wallet.backup
```

**QUERY WALLET BALANCE**

```
allorad q bank balances $(allorad keys show wallet -a)
```

### 👷 Validator management <a href="#validator-management" id="validator-management"></a>

Please make sure you have adjusted **moniker**, **identity**, **details** and **website** to match your values.

```
cd $HOME
# Create validator.json file
echo "{\"pubkey\":{\"@type\":\"/cosmos.crypto.ed25519.PubKey\",\"key\":\"$(allorad comet show-validator | grep -Po '\"key\":\s*\"\K[^"]*')\"},
    \"amount\": \"1000000000000000000uallo\",
    \"moniker\": \"test\",
    \"identity\": \"\",
    \"website\": \"\",
    \"security\": \"\",
    \"details\": \"CoinHunters Community ❤️\",
    \"commission-rate\": \"0.1\",
    \"commission-max-rate\": \"0.2\",
    \"commission-max-change-rate\": \"0.01\",
    \"min-self-delegation\": \"1\"
}" > validator.json
# Create a validator using the JSON configuration
allorad tx staking create-validator validator.json \
    --from wallet \
    chain-id allora-testnet-1 \
	--gas auto --gas-adjustment 1.5 --fees 0.0uallo \
```

**UNJAIL VALIDATOR**

```
allorad tx slashing unjail --from wallet --chain-id allora-testnet-1 --gas-adjustment 1.5 --gas auto --gas-prices 0.0uallo -y
```

**JAIL REASON**

```
allorad query slashing signing-info $(allorad tendermint show-validator)
```

**LIST ALL ACTIVE VALIDATORS**

```
allorad q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

**LIST ALL INACTIVE VALIDATORS**

```
allorad q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_UNBONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

**VIEW VALIDATOR DETAILS**

```
allorad q staking validator $(allorad keys show wallet --bech val -a)
```

### 💲 Token management <a href="#token-management" id="token-management"></a>

**WITHDRAW REWARDS FROM ALL VALIDATORS**

```
allorad tx distribution withdraw-rewards $(allorad keys show wallet --bech val -a) --commission --from wallet --chain-id allora-testnet-1 --gas-adjustment 1.5 --gas auto --gas-prices 0.0uallo -y
```

**WITHDRAW COMMISSION AND REWARDS FROM YOUR VALIDATOR**

```
allorad tx distribution withdraw-rewards $(allorad keys show wallet --bech val -a) --commission --from wallet --chain-id allora-testnet-1 --gas-adjustment 1.5 --gas auto --gas-prices 0.0uallo -y
```

**DELEGATE TOKENS TO YOURSELF**

```
allorad tx staking delegate $(allorad keys show wallet --bech val -a) 1000000000000000000uallo --from wallet --chain-id allora-testnet-1 --gas-adjustment 1.5 --gas auto --gas-prices 0.0uallo -y
```

**DELEGATE TOKENS TO VALIDATOR**

```
allorad tx staking delegate <TO_VALOPER_ADDRESS> 1000000000000000000uallo --from wallet --chain-id allora-testnet-1 --gas-adjustment 1.5 --gas auto --gas-prices 0.0uallo -y
```

**REDELEGATE TOKENS TO ANOTHER VALIDATOR**

```
allorad tx staking redelegate $(allorad keys show wallet --bech val -a) <TO_VALOPER_ADDRESS> 1000000000000000000uallo --from wallet --chain-id allora-testnet-1 --gas-adjustment 1.5 --gas auto --gas-prices 0.0uallo -y
```

**UNBOND TOKENS FROM YOUR VALIDATOR**

```
allorad tx staking unbond $(allorad keys show wallet --bech val -a) 1000000000000000000uallo --from wallet --chain-id allora-testnet-1 --gas-adjustment 1.5 --gas auto --gas-prices 0.0uallo -y
```

**SEND TOKENS TO THE WALLET**

```
allorad tx bank send wallet <TO_WALLET_ADDRESS> 1000000000000000000uallo --from wallet --chain-id allora-testnet-1 --gas-adjustment 1.5 --gas auto --gas-prices 0.0uallo -y
```

### 🗳 Governance <a href="#governance" id="governance"></a>

**LIST ALL PROPOSALS**

```
allorad query gov proposals
```

**VIEW PROPOSAL BY ID**

```
allorad query gov proposal 1
```

**VOTE ‘YES’**

```
allorad tx gov vote 1 yes --from wallet --chain-id allora-testnet-1 --gas-adjustment 1.5 --gas auto --gas-prices 0.0uallo -y
```

**VOTE ‘NO’**

```
allorad tx gov vote 1 no --from wallet --chain-id allora-testnet-1 --gas-adjustment 1.5 --gas auto --gas-prices 0.0uallo -y
```

**VOTE ‘ABSTAIN’**

```
allorad tx gov vote 1 abstain --from wallet --chain-id allora-testnet-1 --gas-adjustment 1.5 --gas auto --gas-prices 0.0uallo -y
```

**VOTE ‘NOWITHVETO’**

```
allorad tx gov vote 1 NoWithVeto --from wallet --chain-id allora-testnet-1 --gas-adjustment 1.5 --gas auto --gas-prices 0.0uallo -y
```

### ⚡️ Utility <a href="#utility" id="utility"></a>

**UPDATE PORTS**

```
UPDATE PORTS
CUSTOM_PORT=110
sed -i -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${CUSTOM_PORT}58\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${CUSTOM_PORT}57\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${CUSTOM_PORT}60\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${CUSTOM_PORT}56\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${CUSTOM_PORT}66\"%" $HOME/.allorad/config/config.toml
sed -i -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:${CUSTOM_PORT}17\"%; s%^address = \":8080\"%address = \":${CUSTOM_PORT}80\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${CUSTOM_PORT}90\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${CUSTOM_PORT}91\"%" $HOME/.allorad/config/app.toml
```

**UPDATE INDEXER**

**Disable indexer**

```
sed -i -e 's|^indexer *=.*|indexer = "null"|' $HOME/.allorad/config/config.toml
```

**Enable indexer**

```
sed -i -e 's|^indexer *=.*|indexer = "kv"|' $HOME/.allorad/config/config.toml
```

**UPDATE PRUNING**

```
sed -i \
  -e 's|^pruning *=.*|pruning = "custom"|' \
  -e 's|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|' \
  -e 's|^pruning-keep-every *=.*|pruning-keep-every = "0"|' \
  -e 's|^pruning-interval *=.*|pruning-interval = "19"|' \
  $HOME/.allorad/config/app.toml
```

### 🚨 Maintenance <a href="#maintenance" id="maintenance"></a>

**GET VALIDATOR INFO**

```
allorad status 2>&1 | jq .ValidatorInfo
```

**GET SYNC INFO**

```
allorad status 2>&1 | jq
```

**GET NODE PEER**

```
echo $(allorad tendermint show-node-id)'@'$(curl -s ifconfig.me)':'$(cat $HOME/.allorad/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```

**CHECK IF VALIDATOR KEY IS CORRECT**

```
[[ $(allorad q staking validator $(allorad keys show wallet --bech val -a) -oj | jq -r .consensus_pubkey.key) = $(allorad status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

**GET LIVE PEERS**

```
curl -sS http://localhost:26657/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}'
```

**SET MINIMUM GAS PRICE**

```
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.0uallo\"/" $HOME/.allorad/config/app.toml
```

**ENABLE PROMETHEUS**

```
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.allorad/config/config.toml
```

**RESET CHAIN DATA**

```
allorad tendermint unsafe-reset-all --keep-addr-book --home $HOME/.allorad --keep-addr-book
```

**REMOVE NODE**

Please, before proceeding with the next step! All chain data will be lost! Make sure you have backed up your **priv\_validator\_key.json**!

```
cd $HOME
sudo systemctl stop allora.service
sudo systemctl disable allora.service
sudo rm /etc/systemd/system/allorad.service
sudo systemctl daemon-reload
rm -f $(which allorad)
rm -rf $HOME/.allorad
```

### ⚙️ Service Management <a href="#service-management" id="service-management"></a>

**RELOAD SERVICE CONFIGURATION**

```
sudo systemctl daemon-reload
```

**ENABLE SERVICE**

```
sudo systemctl enable allora.service
```

**DISABLE SERVICE**

```
sudo systemctl disable allorad
```

**START SERVICE**

```
sudo systemctl start allora.service
```

**STOP SERVICE**

```
sudo systemctl stop allora.service
```

**RESTART SERVICE**

```
sudo systemctl restart allora.service
```

**CHECK SERVICE STATUS**

```
sudo systemctl status allora.service
```

**CHECK SERVICE LOGS**

```
sudo journalctl -u allora.service -f --no-hostname -o cat
```
