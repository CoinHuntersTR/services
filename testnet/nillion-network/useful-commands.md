# Useful commands

### 🔑 Key management <a href="#key-management" id="key-management"></a>

**ADD NEW KEY**

```
nilchaind keys add wallet
```

**RECOVER EXISTING KEY**

```
nilchaind keys add wallet --recover
```

**LIST ALL KEYS**

```
nilchaind keys list
```

**DELETE KEY**

```
nilchaind keys delete wallet
```

**EXPORT KEY TO A FILE**

```
nilchaind keys export wallet
```

**IMPORT KEY FROM A FILE**

```
nilchaind keys import wallet wallet.backup
```

**QUERY WALLET BALANCE**

```
nilchaind q bank balances $(nilchaind keys show wallet -a)
```

### 👷 Validator management <a href="#validator-management" id="validator-management"></a>

Please make sure you have adjusted **moniker**, **identity**, **details** and **website** to match your values.

```
cd $HOME
# Create validator.json file
echo "{\"pubkey\":{\"@type\":\"/cosmos.crypto.ed25519.PubKey\",\"key\":\"$(nilchaind comet show-validator | grep -Po '\"key\":\s*\"\K[^"]*')\"},
    \"amount\": \"1000000unil\",
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
nilchaind tx staking create-validator validator.json \
    --from wallet \
    --chain-id nillion-chain-testnet-1 \
	--gas auto --gas-adjustment 1.5 --fees 0unil \
```

**UNJAIL VALIDATOR**

```
nilchaind tx slashing unjail --from wallet --chain-id nillion-chain-testnet-1 --gas-adjustment 1.5 --gas auto --gas-prices 0unil -y
```

**JAIL REASON**

```
nilchaind query slashing signing-info $(nilchaind tendermint show-validator)
```

**LIST ALL ACTIVE VALIDATORS**

```
nilchaind q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

**LIST ALL INACTIVE VALIDATORS**

```
nilchaind q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_UNBONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

**VIEW VALIDATOR DETAILS**

```
nilchaind q staking validator $(nilchaind keys show wallet --bech val -a)
```

### 💲 Token management <a href="#token-management" id="token-management"></a>

**WITHDRAW REWARDS FROM ALL VALIDATORS**

```
nilchaind tx distribution withdraw-rewards $(nilchaind keys show wallet --bech val -a) --commission --from wallet --chain-id nillion-chain-testnet-1 --gas-adjustment 1.5 --gas auto --gas-prices 0unil -y
```

**WITHDRAW COMMISSION AND REWARDS FROM YOUR VALIDATOR**

```
nilchaind tx distribution withdraw-rewards $(nilchaind keys show wallet --bech val -a) --commission --from wallet --chain-id nillion-chain-testnet-1 --gas-adjustment 1.5 --gas auto --gas-prices 0unil -y
```

**DELEGATE TOKENS TO YOURSELF**

```
nilchaind tx staking delegate $(nilchaind keys show wallet --bech val -a) 1000000unil --from wallet --chain-id nillion-chain-testnet-1 --gas-adjustment 1.5 --gas auto --gas-prices 0unil -y
```

**DELEGATE TOKENS TO VALIDATOR**

```
nilchaind tx staking delegate <TO_VALOPER_ADDRESS> 1000000unil --from wallet --chain-id nillion-chain-testnet-1 --gas-adjustment 1.5 --gas auto --gas-prices 0unil -y
```

**REDELEGATE TOKENS TO ANOTHER VALIDATOR**

```
nilchaind tx staking redelegate $(nilchaind keys show wallet --bech val -a) <TO_VALOPER_ADDRESS> 1000000unil --from wallet --chain-id nillion-chain-testnet-1 --gas-adjustment 1.5 --gas auto --gas-prices 0unil -y
```

**UNBOND TOKENS FROM YOUR VALIDATOR**

```
nilchaind tx staking unbond $(nilchaind keys show wallet --bech val -a) 1000000unil --from wallet --chain-id nillion-chain-testnet-1 --gas-adjustment 1.5 --gas auto --gas-prices 0unil -y
```

**SEND TOKENS TO THE WALLET**

```
nilchaind tx bank send wallet <TO_WALLET_ADDRESS> 1000000unil --from wallet --chain-id nillion-chain-testnet-1 --gas-adjustment 1.5 --gas auto --gas-prices 0unil -y
```

### 🗳 Governance <a href="#governance" id="governance"></a>

**LIST ALL PROPOSALS**

```
nilchaind query gov proposals
```

**VIEW PROPOSAL BY ID**

```
nilchaind query gov proposal 1
```

**VOTE ‘YES’**

```
nilchaind tx gov vote 1 yes --from wallet --chain-id nillion-chain-testnet-1 --gas-adjustment 1.5 --gas auto --gas-prices 0unil -y
```

**VOTE ‘NO’**

```
nilchaind tx gov vote 1 no --from wallet --chain-id nillion-chain-testnet-1 --gas-adjustment 1.5 --gas auto --gas-prices 0unil -y
```

**VOTE ‘ABSTAIN’**

```
nilchaind tx gov vote 1 abstain --from wallet --chain-id nillion-chain-testnet-1 --gas-adjustment 1.5 --gas auto --gas-prices 0unil -y
```

**VOTE ‘NOWITHVETO’**

```
nilchaind tx gov vote 1 NoWithVeto --from wallet --chain-id nillion-chain-testnet-1 --gas-adjustment 1.5 --gas auto --gas-prices 0unil -y
```

### ⚡️ Utility <a href="#utility" id="utility"></a>

**UPDATE PORTS**

```
UPDATE PORTS
CUSTOM_PORT=120
sed -i -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${CUSTOM_PORT}58\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${CUSTOM_PORT}57\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${CUSTOM_PORT}60\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${CUSTOM_PORT}56\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${CUSTOM_PORT}66\"%" $HOME/.nillionapp/config/config.toml
sed -i -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:${CUSTOM_PORT}17\"%; s%^address = \":8080\"%address = \":${CUSTOM_PORT}80\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${CUSTOM_PORT}90\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${CUSTOM_PORT}91\"%" $HOME/.nillionapp/config/app.toml
```

**UPDATE INDEXER**

**Disable indexer**

```
sed -i -e 's|^indexer *=.*|indexer = "null"|' $HOME/.nillionapp/config/config.toml
```

**Enable indexer**

```
sed -i -e 's|^indexer *=.*|indexer = "kv"|' $HOME/.nillionapp/config/config.toml
```

**UPDATE PRUNING**

```
sed -i \
  -e 's|^pruning *=.*|pruning = "custom"|' \
  -e 's|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|' \
  -e 's|^pruning-keep-every *=.*|pruning-keep-every = "0"|' \
  -e 's|^pruning-interval *=.*|pruning-interval = "19"|' \
  $HOME/.nillionapp/config/app.toml
```

### 🚨 Maintenance <a href="#maintenance" id="maintenance"></a>

**GET VALIDATOR INFO**

```
nilchaind status 2>&1 | jq .ValidatorInfo
```

**GET SYNC INFO**

```
nilchaind status 2>&1 | jq
```

**GET NODE PEER**

```
echo $(nilchaind tendermint show-node-id)'@'$(curl -s ifconfig.me)':'$(cat $HOME/.nillionapp/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```

**CHECK IF VALIDATOR KEY IS CORRECT**

```
[[ $(nilchaind q staking validator $(nilchaind keys show wallet --bech val -a) -oj | jq -r .consensus_pubkey.key) = $(nilchaind status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

**GET LIVE PEERS**

```
curl -sS http://localhost:26657/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}'
```

**SET MINIMUM GAS PRICE**

```
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0unil\"/" $HOME/.nillionapp/config/app.toml
```

**ENABLE PROMETHEUS**

```
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.nillionapp/config/config.toml
```

**RESET CHAIN DATA**

```
nilchaind tendermint unsafe-reset-all --keep-addr-book --home $HOME/.nillionapp --keep-addr-book
```

**REMOVE NODE**

Please, before proceeding with the next step! All chain data will be lost! Make sure you have backed up your **priv\_validator\_key.json**!

```
cd $HOME
sudo systemctl stop nillion-testnet.service
sudo systemctl disable nillion-testnet.service
sudo rm /etc/systemd/system/nilchaind
sudo systemctl daemon-reload
rm -f $(which nilchaind)
rm -rf $HOME/.nillionapp
rm -rf $HOME/nillionapp
```

### ⚙️ Service Management <a href="#service-management" id="service-management"></a>

**RELOAD SERVICE CONFIGURATION**

```
sudo systemctl daemon-reload
```

**ENABLE SERVICE**

```
sudo systemctl enable nillion-testnet.service
```

**DISABLE SERVICE**

```
sudo systemctl disable nilchaind
```

**START SERVICE**

```
sudo systemctl start nillion-testnet.service
```

**STOP SERVICE**

```
sudo systemctl stop nillion-testnet.service
```

**RESTART SERVICE**

```
sudo systemctl restart nillion-testnet.service
```

**CHECK SERVICE STATUS**

```
sudo systemctl status nillion-testnet.service
```

**CHECK SERVICE LOGS**

```
sudo journalctl -u nillion-testnet.service -f --no-hostname -o cat
```
