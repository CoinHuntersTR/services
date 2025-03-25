# Useful commands

### 🔑 Key management <a href="#key-management" id="key-management"></a>

**ADD NEW KEY**

```
junctiond keys add wallet
```

**RECOVER EXISTING KEY**

```
junctiond keys add wallet --recover
```

**LIST ALL KEYS**

```
junctiond keys list
```

**DELETE KEY**

```
junctiond keys delete wallet
```

**EXPORT KEY TO A FILE**

```
junctiond keys export wallet
```

**IMPORT KEY FROM A FILE**

```
junctiond keys import wallet wallet.backup
```

**QUERY WALLET BALANCE**

```
junctiond q bank balances $(junctiond keys show wallet -a)
```

### 👷 Validator management <a href="#validator-management" id="validator-management"></a>

Please make sure you have adjusted **moniker**, **identity**, **details** and **website** to match your values.

```
cd $HOME
# Create validator.json file
echo "{\"pubkey\":{\"@type\":\"/cosmos.crypto.ed25519.PubKey\",\"key\":\"$(junctiond comet show-validator | grep -Po '\"key\":\s*\"\K[^"]*')\"},
    \"amount\": \"100000000000uamf\",
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
junctiond tx staking create-validator validator.json \
    --from wallet \
    --chain-id varanasi-1 \
	--gas auto --gas-adjustment 1.5 --fees 0.001uamf \
```

**UNJAIL VALIDATOR**

```
junctiond tx slashing unjail --from wallet --chain-id varanasi-1 --gas-adjustment 1.5 --gas auto --gas-prices 0.001uamf -y
```

**JAIL REASON**

```
junctiond query slashing signing-info $(junctiond tendermint show-validator)
```

**LIST ALL ACTIVE VALIDATORS**

```
junctiond q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

**LIST ALL INACTIVE VALIDATORS**

```
junctiond q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_UNBONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

**VIEW VALIDATOR DETAILS**

```
junctiond q staking validator $(junctiond keys show wallet --bech val -a)
```

### 💲 Token management <a href="#token-management" id="token-management"></a>

**WITHDRAW REWARDS FROM ALL VALIDATORS**

```
junctiond tx distribution withdraw-rewards $(junctiond keys show wallet --bech val -a) --commission --from wallet --chain-id varanasi-1 --gas-adjustment 1.5 --gas auto --gas-prices 0.001uamf -y
```

**WITHDRAW COMMISSION AND REWARDS FROM YOUR VALIDATOR**

```
junctiond tx distribution withdraw-rewards $(junctiond keys show wallet --bech val -a) --commission --from wallet --chain-id varanasi-1 --gas-adjustment 1.5 --gas auto --gas-prices 0.001uamf -y
```

**DELEGATE TOKENS TO YOURSELF**

```
junctiond tx staking delegate $(junctiond keys show wallet --bech val -a) 100000000000uamf --from wallet --chain-id varanasi-1 --gas-adjustment 1.5 --gas auto --gas-prices 0.001uamf -y
```

**DELEGATE TOKENS TO VALIDATOR**

```
junctiond tx staking delegate <TO_VALOPER_ADDRESS> 100000000000uamf --from wallet --chain-id varanasi-1 --gas-adjustment 1.5 --gas auto --gas-prices 0.001uamf -y
```

**REDELEGATE TOKENS TO ANOTHER VALIDATOR**

```
junctiond tx staking redelegate $(junctiond keys show wallet --bech val -a) <TO_VALOPER_ADDRESS> 100000000000uamf --from wallet --chain-id varanasi-1 --gas-adjustment 1.5 --gas auto --gas-prices 0.001uamf -y
```

**UNBOND TOKENS FROM YOUR VALIDATOR**

```
junctiond tx staking unbond $(junctiond keys show wallet --bech val -a) 100000000000uamf --from wallet --chain-id varanasi-1 --gas-adjustment 1.5 --gas auto --gas-prices 0.001uamf -y
```

**SEND TOKENS TO THE WALLET**

```
junctiond tx bank send wallet <TO_WALLET_ADDRESS> 100000000000uamf --from wallet --chain-id varanasi-1 --gas-adjustment 1.5 --gas auto --gas-prices 0.001uamf -y
```

### 🗳 Governance <a href="#governance" id="governance"></a>

**LIST ALL PROPOSALS**

```
junctiond query gov proposals
```

**VIEW PROPOSAL BY ID**

```
junctiond query gov proposal 1
```

**VOTE ‘YES’**

```
junctiond tx gov vote 1 yes --from wallet --chain-id varanasi-1 --gas-adjustment 1.5 --gas auto --gas-prices 0.001uamf -y
```

**VOTE ‘NO’**

```
junctiond tx gov vote 1 no --from wallet --chain-id varanasi-1 --gas-adjustment 1.5 --gas auto --gas-prices 0.001uamf -y
```

**VOTE ‘ABSTAIN’**

```
junctiond tx gov vote 1 abstain --from wallet --chain-id varanasi-1 --gas-adjustment 1.5 --gas auto --gas-prices 0.001uamf -y
```

**VOTE ‘NOWITHVETO’**

```
junctiond tx gov vote 1 NoWithVeto --from wallet --chain-id varanasi-1 --gas-adjustment 1.5 --gas auto --gas-prices 0.001uamf -y
```

### ⚡️ Utility <a href="#utility" id="utility"></a>

**UPDATE PORTS**

```
UPDATE PORTS
CUSTOM_PORT=110
sed -i -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${CUSTOM_PORT}58\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${CUSTOM_PORT}57\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${CUSTOM_PORT}60\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${CUSTOM_PORT}56\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${CUSTOM_PORT}66\"%" $HOME/.junctiond/config/config.toml
sed -i -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:${CUSTOM_PORT}17\"%; s%^address = \":8080\"%address = \":${CUSTOM_PORT}80\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${CUSTOM_PORT}90\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${CUSTOM_PORT}91\"%" $HOME/.junctiond/config/app.toml
```

**UPDATE INDEXER**

**Disable indexer**

```
sed -i -e 's|^indexer *=.*|indexer = "null"|' $HOME/.junctiond/config/config.toml
```

**Enable indexer**

```
sed -i -e 's|^indexer *=.*|indexer = "kv"|' $HOME/.junctiond/config/config.toml
```

**UPDATE PRUNING**

```
sed -i \
  -e 's|^pruning *=.*|pruning = "custom"|' \
  -e 's|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|' \
  -e 's|^pruning-keep-every *=.*|pruning-keep-every = "0"|' \
  -e 's|^pruning-interval *=.*|pruning-interval = "19"|' \
  $HOME/.junctiond/config/app.toml
```

### 🚨 Maintenance <a href="#maintenance" id="maintenance"></a>

**GET VALIDATOR INFO**

```
junctiond status 2>&1 | jq .ValidatorInfo
```

**GET SYNC INFO**

```
junctiond status 2>&1 | jq
```

**GET NODE PEER**

```
echo $(junctiond tendermint show-node-id)'@'$(curl -s ifconfig.me)':'$(cat $HOME/.junctiond/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```

**CHECK IF VALIDATOR KEY IS CORRECT**

```
[[ $(junctiond q staking validator $(junctiond keys show wallet --bech val -a) -oj | jq -r .consensus_pubkey.key) = $(junctiond status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

**GET LIVE PEERS**

```
curl -sS http://localhost:26657/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}'
```

**SET MINIMUM GAS PRICE**

```
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.001uamf\"/" $HOME/.junctiond/config/app.toml
```

**ENABLE PROMETHEUS**

```
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.junctiond/config/config.toml
```

**RESET CHAIN DATA**

```
junctiond tendermint unsafe-reset-all --keep-addr-book --home $HOME/.junctiond --keep-addr-book
```

**REMOVE NODE**

Please, before proceeding with the next step! All chain data will be lost! Make sure you have backed up your **priv\_validator\_key.json**!

```
cd $HOME
sudo systemctl stop junctiond
sudo systemctl disable junctiond
sudo rm /etc/systemd/system/junctiond.service
sudo systemctl daemon-reload
rm -f $(which junctiond)
rm -rf $HOME/.junctiond
```

### ⚙️ Service Management <a href="#service-management" id="service-management"></a>

**RELOAD SERVICE CONFIGURATION**

```
sudo systemctl daemon-reload
```

**ENABLE SERVICE**

```
sudo systemctl enable junctiond
```

**DISABLE SERVICE**

```
sudo systemctl disable junctiond
```

**START SERVICE**

```
sudo systemctl start junctiond
```

**STOP SERVICE**

```
sudo systemctl stop junctiond
```

**RESTART SERVICE**

```
sudo systemctl restart junctiond
```

**CHECK SERVICE STATUS**

```
sudo systemctl status junctiond
```

**CHECK SERVICE LOGS**

```
sudo journalctl -u junctiond -f --no-hostname -o cat
```
