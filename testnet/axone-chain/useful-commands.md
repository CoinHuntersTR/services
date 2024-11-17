# Useful commands

### 🔑 Key management <a href="#key-management" id="key-management"></a>

**ADD NEW KEY**

```
axoned keys add wallet
```

**RECOVER EXISTING KEY**

```
axoned keys add wallet --recover
```

**LIST ALL KEYS**

```
axoned keys list
```

**DELETE KEY**

```
axoned keys delete wallet
```

**EXPORT KEY TO A FILE**

```
axoned keys export wallet
```

**IMPORT KEY FROM A FILE**

```
axoned keys import wallet wallet.backup
```

**QUERY WALLET BALANCE**

```
axoned q bank balances $(axoned keys show wallet -a)
```

### 👷 Validator management <a href="#validator-management" id="validator-management"></a>

Please make sure you have adjusted **moniker**, **identity**, **details** and **website** to match your values.

```
cd $HOME
# Create validator.json file
echo "{\"pubkey\":{\"@type\":\"/cosmos.crypto.ed25519.PubKey\",\"key\":\"$(axoned comet show-validator | grep -Po '\"key\":\s*\"\K[^"]*')\"},
    \"amount\": \"1000000uaxone\",
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
axoned tx staking create-validator validator.json \
    --from wallet \
    chain-id axone-dentrite-1 \
	--gas auto --gas-adjustment 1.5 --fees 0uaxone \
```

**UNJAIL VALIDATOR**

```
axoned tx slashing unjail --from wallet chain-id axone-dentrite-1 --gas-adjustment 1.5 --gas auto --gas-prices 0uaxone -y
```

**JAIL REASON**

```
axoned query slashing signing-info $(axoned tendermint show-validator)
```

**LIST ALL ACTIVE VALIDATORS**

```
axoned q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

**LIST ALL INACTIVE VALIDATORS**

```
axoned q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_UNBONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

**VIEW VALIDATOR DETAILS**

```
axoned q staking validator $(axoned keys show wallet --bech val -a)
```

### 💲 Token management <a href="#token-management" id="token-management"></a>

**WITHDRAW REWARDS FROM ALL VALIDATORS**

```
axoned tx distribution withdraw-rewards $(axoned keys show wallet --bech val -a) --commission --from wallet chain-id axone-dentrite-1 --gas-adjustment 1.5 --gas auto --gas-prices 0uaxone -y
```

**WITHDRAW COMMISSION AND REWARDS FROM YOUR VALIDATOR**

```
axoned tx distribution withdraw-rewards $(axoned keys show wallet --bech val -a) --commission --from wallet --chain-id axone-dentrite-1 --gas-adjustment 1.5 --gas auto --gas-prices 0uaxone -y
```

**DELEGATE TOKENS TO YOURSELF**

```
axoned tx staking delegate $(axoned keys show wallet --bech val -a) 1000000uaxone --from wallet --chain-id axone-dentrite-1 --gas-adjustment 1.5 --gas auto --gas-prices 0uaxone -y
```

**DELEGATE TOKENS TO VALIDATOR**

```
axoned tx staking delegate <TO_VALOPER_ADDRESS> 1000000uaxone --from wallet --chain-id axone-dentrite-1 --gas-adjustment 1.5 --gas auto --gas-prices 0uaxone -y
```

**REDELEGATE TOKENS TO ANOTHER VALIDATOR**

```
axoned tx staking redelegate $(axoned keys show wallet --bech val -a) <TO_VALOPER_ADDRESS> 1000000uaxone --from wallet --chain-id axone-dentrite-1 --gas-adjustment 1.5 --gas auto --gas-prices 0uaxone -y
```

**UNBOND TOKENS FROM YOUR VALIDATOR**

```
axoned tx staking unbond $(axoned keys show wallet --bech val -a) 1000000uaxone --from wallet --chain-id axone-dentrite-1 --gas-adjustment 1.5 --gas auto --gas-prices 0uaxone -y
```

**SEND TOKENS TO THE WALLET**

```
axoned tx bank send wallet <TO_WALLET_ADDRESS> 1000000uaxone --from wallet --chain-id axone-dentrite-1 --gas-adjustment 1.5 --gas auto --gas-prices 0uaxone -y
```

### 🗳 Governance <a href="#governance" id="governance"></a>

**LIST ALL PROPOSALS**

```
axoned query gov proposals
```

**VIEW PROPOSAL BY ID**

```
axoned query gov proposal 1
```

**VOTE ‘YES’**

```
axoned tx gov vote 1 yes --from wallet --chain-id axone-dentrite-1 --gas-adjustment 1.5 --gas auto --gas-prices 0uaxone -y
```

**VOTE ‘NO’**

```
axoned tx gov vote 1 no --from wallet --chain-id axone-dentrite-1 --gas-adjustment 1.5 --gas auto --gas-prices 0uaxone -y
```

**VOTE ‘ABSTAIN’**

```
axoned tx gov vote 1 abstain --from wallet --chain-id axone-dentrite-1 --gas-adjustment 1.5 --gas auto --gas-prices 0uaxone -y
```

**VOTE ‘NOWITHVETO’**

```
axoned tx gov vote 1 NoWithVeto --from wallet --chain-id axone-dentrite-1 --gas-adjustment 1.5 --gas auto --gas-prices 0uaxone -y
```

### ⚡️ Utility <a href="#utility" id="utility"></a>

**UPDATE PORTS**

```
UPDATE PORTS
CUSTOM_PORT=110
sed -i -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${CUSTOM_PORT}58\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${CUSTOM_PORT}57\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${CUSTOM_PORT}60\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${CUSTOM_PORT}56\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${CUSTOM_PORT}66\"%" $HOME/.axoned/config/config.toml
sed -i -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:${CUSTOM_PORT}17\"%; s%^address = \":8080\"%address = \":${CUSTOM_PORT}80\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${CUSTOM_PORT}90\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${CUSTOM_PORT}91\"%" $HOME/.axoned/config/app.toml
```

**UPDATE INDEXER**

**Disable indexer**

```
sed -i -e 's|^indexer *=.*|indexer = "null"|' $HOME/.axoned/config/config.toml
```

**Enable indexer**

```
sed -i -e 's|^indexer *=.*|indexer = "kv"|' $HOME/.axoned/config/config.toml
```

**UPDATE PRUNING**

```
sed -i \
  -e 's|^pruning *=.*|pruning = "custom"|' \
  -e 's|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|' \
  -e 's|^pruning-keep-every *=.*|pruning-keep-every = "0"|' \
  -e 's|^pruning-interval *=.*|pruning-interval = "19"|' \
  $HOME/.axoned/config/app.toml
```

### 🚨 Maintenance <a href="#maintenance" id="maintenance"></a>

**GET VALIDATOR INFO**

```
axoned status 2>&1 | jq .ValidatorInfo
```

**GET SYNC INFO**

```
axoned status 2>&1 | jq
```

**GET NODE PEER**

```
echo $(axoned tendermint show-node-id)'@'$(curl -s ifconfig.me)':'$(cat $HOME/.axoned/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```

**CHECK IF VALIDATOR KEY IS CORRECT**

```
[[ $(axoned q staking validator $(axoned keys show wallet --bech val -a) -oj | jq -r .consensus_pubkey.key) = $(axoned status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

**GET LIVE PEERS**

```
curl -sS http://localhost:26657/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}'
```

**SET MINIMUM GAS PRICE**

```
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0uaxone\"/" $HOME/.axoned/config/app.toml
```

**ENABLE PROMETHEUS**

```
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.axoned/config/config.toml
```

**RESET CHAIN DATA**

```
axoned tendermint unsafe-reset-all --keep-addr-book --home $HOME/.axoned --keep-addr-book
```

**REMOVE NODE**

Please, before proceeding with the next step! All chain data will be lost! Make sure you have backed up your **priv\_validator\_key.json**!

```
cd $HOME
sudo systemctl stop axoned
sudo systemctl disable axoned
sudo rm /etc/systemd/system/axoned.service
sudo systemctl daemon-reload
rm -f $(which axoned)
rm -rf $HOME/.axoned
rm -rf $HOME/axoned
```

### ⚙️ Service Management <a href="#service-management" id="service-management"></a>

**RELOAD SERVICE CONFIGURATION**

```
sudo systemctl daemon-reload
```

**ENABLE SERVICE**

```
sudo systemctl enable axoned
```

**DISABLE SERVICE**

```
sudo systemctl disable axoned
```

**START SERVICE**

```
sudo systemctl start axoned
```

**STOP SERVICE**

```
sudo systemctl stop axoned
```

**RESTART SERVICE**

```
sudo systemctl restart axoned
```

**CHECK SERVICE STATUS**

```
sudo systemctl status axoned
```

**CHECK SERVICE LOGS**

```
sudo journalctl -u axoned -f --no-hostname -o cat
```
