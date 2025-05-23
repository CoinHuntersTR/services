# Useful commands

### 🔑 Key management <a href="#key-management" id="key-management"></a>

**ADD NEW KEY**

```
babylond keys add wallet
```

**RECOVER EXISTING KEY**

```
babylond keys add wallet --recover
```

**LIST ALL KEYS**

```
babylond keys list
```

**DELETE KEY**

```
babylond keys delete wallet
```

**EXPORT KEY TO A FILE**

```
babylond keys export wallet
```

**IMPORT KEY FROM A FILE**

```
babylond keys import wallet wallet.backup
```

**QUERY WALLET BALANCE**

```
babylond q bank balances $(babylond keys show wallet -a)
```

### 👷 Validator management <a href="#validator-management" id="validator-management"></a>

Please make sure you have adjusted **moniker**, **identity**, **details** and **website** to match your values.

**CREATE NEW VALIDATOR**

```
cat << EOF > ~/validator.json
{   
    "pubkey":{"@type":"/cosmos.crypto.ed25519.PubKey","key":"wHOuViZC5L6fhOrYzFDAx9ySC2GOW61im6u/mP/Urh0="},
    "amount": "1000000ubbn",
    "moniker": "",
    "identity": "",
    "website": "",
    "security": "",
    "details": "",
    "commission-rate": "0.1",
    "commission-max-rate": "0.2",
    "commission-max-change-rate": "0.01",
    "min-self-delegation": "1"
}
EOF
```

**EDIT EXISTING VALIDATOR**

```
babylond tx staking edit-validator \
--new-moniker "YOUR_MONIKER_NAME" \
--identity "YOUR_KEYBASE_ID" \
--details "YOUR_DETAILS" \
--website "YOUR_WEBSITE_URL" \
--chain-id bbn-1 \
--commission-rate 0.05 \
--from wallet \
--gas-adjustment 1.5 \
--gas auto \
--gas-prices  0.025ubbn \
-y
```

**UNJAIL VALIDATOR**

```
babylond tx slashing unjail --from wallet --chain-id bbn-1 --gas-adjustment 1.5 --gas auto --gas-prices  0.025ubbn -y
```

**JAIL REASON**

```
babylond query slashing signing-info $(babylond tendermint show-validator)
```

**LIST ALL ACTIVE VALIDATORS**

```
babylond q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

**LIST ALL INACTIVE VALIDATORS**

```
babylond q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_UNBONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

**VIEW VALIDATOR DETAILS**

```
babylond q staking validator $(babylond keys show wallet --bech val -a)
```

### 💲 Token management <a href="#token-management" id="token-management"></a>

**WITHDRAW REWARDS FROM ALL VALIDATORS**

```
babylond tx distribution withdraw-all-rewards --from wallet --chain-id bbn-1 --gas-adjustment 1.5 --gas auto --gas-prices  0.025ubbn -y
```

**WITHDRAW COMMISSION AND REWARDS FROM YOUR VALIDATOR**

```
babylond tx distribution withdraw-rewards $(babylond keys show wallet --bech val -a) --commission --from wallet --chain-id bbn-1 --gas-adjustment 1.5 --gas auto --gas-prices  0.025ubbn -y
```

**DELEGATE TOKENS TO YOURSELF**

```
babylond tx staking delegate $(babylond keys show wallet --bech val -a) 1000000ubbn --from wallet --chain-id bbn-1 --gas-adjustment 1.5 --gas auto --gas-prices  0.025ubbn -y
```

**DELEGATE TOKENS TO VALIDATOR**

```
babylond tx staking delegate <TO_VALOPER_ADDRESS> 1000000ubbn --from wallet --chain-id bbn-1 --gas-adjustment 1.5 --gas auto --gas-prices  0.025ubbn -y
```

**REDELEGATE TOKENS TO ANOTHER VALIDATOR**

```
babylond tx staking redelegate $(babylond keys show wallet --bech val -a) <TO_VALOPER_ADDRESS> 1000000ubbn --from wallet --chain-id bbn-1 --gas-adjustment 1.5 --gas auto --gas-prices  0.025ubbn -y
```

**UNBOND TOKENS FROM YOUR VALIDATOR**

```
babylond tx staking unbond $(babylond keys show wallet --bech val -a) 1000000ubbn --from wallet --chain-id bbn-1 --gas-adjustment 1.5 --gas auto --gas-prices  0.025ubbn -y
```

**SEND TOKENS TO THE WALLET**

```
babylond tx bank send wallet <TO_WALLET_ADDRESS> 1000000ubbn --from wallet --chain-id bbn-1 --gas-adjustment 1.5 --gas auto --gas-prices  0.025ubbn -y
```

### 🗳 Governance <a href="#governance" id="governance"></a>

**LIST ALL PROPOSALS**

```
babylond query gov proposals
```

**VIEW PROPOSAL BY ID**

```
babylond query gov proposal 1
```

**VOTE ‘YES’**

```
babylond tx gov vote 1 yes --from wallet --chain-id bbn-1 --gas-adjustment 1.5 --gas auto --gas-prices  0.025ubbn -y
```

**VOTE ‘NO’**

```
babylond tx gov vote 1 no --from wallet --chain-id bbn-1 --gas-adjustment 1.5 --gas auto --gas-prices  0.025ubbn -y
```

**VOTE ‘ABSTAIN’**

```
babylond tx gov vote 1 abstain --from wallet --chain-id bbn-1 --gas-adjustment 1.5 --gas auto --gas-prices  0.025ubbn -y
```

**VOTE ‘NOWITHVETO’**

```
babylond tx gov vote 1 NoWithVeto --from wallet --chain-id bbn-1 --gas-adjustment 1.5 --gas auto --gas-prices  0.025ubbn -y
```

### ⚡️ Utility <a href="#utility" id="utility"></a>

**UPDATE PORTS**

```
UPDATE PORTS
CUSTOM_PORT=110
sed -i -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${CUSTOM_PORT}58\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${CUSTOM_PORT}57\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${CUSTOM_PORT}60\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${CUSTOM_PORT}56\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${CUSTOM_PORT}66\"%" $HOME/.babylond/config/config.toml
sed -i -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:${CUSTOM_PORT}17\"%; s%^address = \":8080\"%address = \":${CUSTOM_PORT}80\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${CUSTOM_PORT}90\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${CUSTOM_PORT}91\"%" $HOME/.babylond/config/app.toml
```

**UPDATE INDEXER**

**Disable indexer**

```
sed -i -e 's|^indexer *=.*|indexer = "null"|' $HOME/.babylond/config/config.toml
```

**Enable indexer**

```
sed -i -e 's|^indexer *=.*|indexer = "kv"|' $HOME/.babylond/config/config.toml
```

**UPDATE PRUNING**

```
sed -i \
  -e 's|^pruning *=.*|pruning = "custom"|' \
  -e 's|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|' \
  -e 's|^pruning-keep-every *=.*|pruning-keep-every = "0"|' \
  -e 's|^pruning-interval *=.*|pruning-interval = "19"|' \
  $HOME/.babylond/config/app.toml
```

### 🚨 Maintenance <a href="#maintenance" id="maintenance"></a>

**GET VALIDATOR INFO**

```
babylond status 2>&1 | jq .ValidatorInfo
```

**GET SYNC INFO**

```
babylond status 2>&1 | jq
```

**GET NODE PEER**

```
echo $(babylond tendermint show-node-id)'@'$(curl -s ifconfig.me)':'$(cat $HOME/.babylond/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```

**CHECK IF VALIDATOR KEY IS CORRECT**

```
[[ $(babylond q staking validator $(babylond keys show wallet --bech val -a) -oj | jq -r .consensus_pubkey.key) = $(babylond status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

**GET LIVE PEERS**

```
curl -sS http://localhost:14657/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}'
```

**SET MINIMUM GAS PRICE**

```
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.002ubbn\"/" $HOME/.babylond/config/app.toml
```

**ENABLE PROMETHEUS**

```
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.babylond/config/config.toml
```

**RESET CHAIN DATA**

```
babylond tendermint unsafe-reset-all --keep-addr-book --home $HOME/babylond --keep-addr-book
```

**REMOVE NODE**

Please, before proceeding with the next step! All chain data will be lost! Make sure you have backed up your **priv\_validator\_key.json**!

```
cd $HOME
sudo systemctl stop babylond
sudo systemctl disable babylond
sudo rm /etc/systemd/system/babylond
sudo systemctl daemon-reload
rm -f $(which babylond)
rm -rf $HOME/.babylond
```

### ⚙️ Service Management <a href="#service-management" id="service-management"></a>

**RELOAD SERVICE CONFIGURATION**

```
sudo systemctl daemon-reload
```

**ENABLE SERVICE**

```
sudo systemctl enable babylond
```

**DISABLE SERVICE**

```
sudo systemctl disable babylond
```

**START SERVICE**

```
sudo systemctl start babylond
```

**STOP SERVICE**

```
sudo systemctl stop babylond
```

**RESTART SERVICE**

```
sudo systemctl restart babylond
```

**CHECK SERVICE STATUS**

```
sudo systemctl status babylond
```

**CHECK SERVICE LOGS**

```
sudo journalctl -u babylond -f --no-hostname -o cat
```
