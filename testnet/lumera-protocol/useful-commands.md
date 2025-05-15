# Useful commands

### 🔑 Key management <a href="#key-management" id="key-management"></a>

**ADD NEW KEY**

```
mantrachaind keys add wallet
```

**RECOVER EXISTING KEY**

```
mantrachaind keys add wallet --recover
```

**LIST ALL KEYS**

```
mantrachaind keys list
```

**DELETE KEY**

```
mantrachaind keys delete wallet
```

**EXPORT KEY TO A FILE**

```
mantrachaind keys export wallet
```

**IMPORT KEY FROM A FILE**

```
mantrachaind keys import wallet wallet.backup
```

**QUERY WALLET BALANCE**

```
mantrachaind q bank balances $(mantrachaind keys show wallet -a)
```

### 👷 Validator management <a href="#validator-management" id="validator-management"></a>

Please make sure you have adjusted **moniker**, **identity**, **details** and **website** to match your values.

**CREATE NEW VALIDATOR**

```
mantrachaind tx staking create-validator \
--amount 1000000uom \
--from $WALLET \
--commission-rate 0.1 \
--commission-max-rate 0.2 \
--commission-max-change-rate 0.01 \
--min-self-delegation 1 \
--pubkey $(mantrachaind tendermint show-validator) \
--moniker "test" \
--identity "" \
--website "" \
--details "I love blockchain ❤️" \
--chain-id mantra-dukong-1 \
--gas auto --gas-adjustment 1.5 --gas-prices  50uom \
-y
```

**EDIT EXISTING VALIDATOR**

```
mantrachaind tx staking edit-validator \
--new-moniker "YOUR_MONIKER_NAME" \
--identity "YOUR_KEYBASE_ID" \
--details "YOUR_DETAILS" \
--website "YOUR_WEBSITE_URL" \
--chain-id mantra-dukong-1 \
--commission-rate 0.05 \
--from wallet \
--gas-adjustment 1.5 \
--gas auto \
--gas-prices  50uom \
-y
```

**UNJAIL VALIDATOR**

```
mantrachaind tx slashing unjail --from wallet --chain-id mantra-dukong-1 --gas-adjustment 1.5 --gas auto --gas-prices  50uom -y
```

**JAIL REASON**

```
mantrachaind query slashing signing-info $(mantrachaind tendermint show-validator)
```

**LIST ALL ACTIVE VALIDATORS**

```
mantrachaind q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

**LIST ALL INACTIVE VALIDATORS**

```
mantrachaind q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_UNBONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

**VIEW VALIDATOR DETAILS**

```
mantrachaind q staking validator $(mantrachaind keys show wallet --bech val -a)
```

### 💲 Token management <a href="#token-management" id="token-management"></a>

**WITHDRAW REWARDS FROM ALL VALIDATORS**

```
mantrachaind tx distribution withdraw-all-rewards --from wallet --chain-id mantra-dukong-1 --gas-adjustment 1.5 --gas auto --gas-prices  50uom -y
```

**WITHDRAW COMMISSION AND REWARDS FROM YOUR VALIDATOR**

```
mantrachaind tx distribution withdraw-rewards $(mantrachaind keys show wallet --bech val -a) --commission --from wallet --chain-id mantra-dukong-1 --gas-adjustment 1.5 --gas auto --gas-prices  50uom -y
```

**DELEGATE TOKENS TO YOURSELF**

```
mantrachaind tx staking delegate $(mantrachaind keys show wallet --bech val -a) 1000000uom --from wallet --chain-id mantra-dukong-1 --gas-adjustment 1.5 --gas auto --gas-prices  50uom -y
```

**DELEGATE TOKENS TO VALIDATOR**

```
mantrachaind tx staking delegate <TO_VALOPER_ADDRESS> 1000000uom --from wallet --chain-id mantra-dukong-1 --gas-adjustment 1.5 --gas auto --gas-prices  50uom -y
```

**REDELEGATE TOKENS TO ANOTHER VALIDATOR**

```
mantrachaind tx staking redelegate $(mantrachaind keys show wallet --bech val -a) <TO_VALOPER_ADDRESS> 1000000uom --from wallet --chain-id mantra-dukong-1 --gas-adjustment 1.5 --gas auto --gas-prices  50uom -y
```

**UNBOND TOKENS FROM YOUR VALIDATOR**

```
mantrachaind tx staking unbond $(mantrachaind keys show wallet --bech val -a) 1000000uom --from wallet --chain-id mantra-dukong-1 --gas-adjustment 1.5 --gas auto --gas-prices  50uom -y
```

**SEND TOKENS TO THE WALLET**

```
mantrachaind tx bank send wallet <TO_WALLET_ADDRESS> 1000000uom --from wallet --chain-id mantra-dukong-1 --gas-adjustment 1.5 --gas auto --gas-prices  50uom -y
```

### 🗳 Governance <a href="#governance" id="governance"></a>

**LIST ALL PROPOSALS**

```
mantrachaind query gov proposals
```

**VIEW PROPOSAL BY ID**

```
mantrachaind query gov proposal 1
```

**VOTE ‘YES’**

```
mantrachaind tx gov vote 1 yes --from wallet --chain-id mantra-dukong-1 --gas-adjustment 1.5 --gas auto --gas-prices  50uom -y
```

**VOTE ‘NO’**

```
mantrachaind tx gov vote 1 no --from wallet --chain-id mantra-dukong-1 --gas-adjustment 1.5 --gas auto --gas-prices  50uom -y
```

**VOTE ‘ABSTAIN’**

```
mantrachaind tx gov vote 1 abstain --from wallet --chain-id mantra-dukong-1 --gas-adjustment 1.5 --gas auto --gas-prices  50uom -y
```

**VOTE ‘NOWITHVETO’**

```
mantrachaind tx gov vote 1 NoWithVeto --from wallet --chain-id mantra-dukong-1 --gas-adjustment 1.5 --gas auto --gas-prices  50uom -y
```

### ⚡️ Utility <a href="#utility" id="utility"></a>

**UPDATE PORTS**

```
UPDATE PORTS
CUSTOM_PORT=110
sed -i -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${CUSTOM_PORT}58\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${CUSTOM_PORT}57\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${CUSTOM_PORT}60\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${CUSTOM_PORT}56\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${CUSTOM_PORT}66\"%" $HOME/.mantrachain/config/config.toml
sed -i -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:${CUSTOM_PORT}17\"%; s%^address = \":8080\"%address = \":${CUSTOM_PORT}80\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${CUSTOM_PORT}90\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${CUSTOM_PORT}91\"%" $HOME/.mantrachain/config/app.toml
```

**UPDATE INDEXER**

**Disable indexer**

```
sed -i -e 's|^indexer *=.*|indexer = "null"|' $HOME/.mantrachain/config/config.toml
```

**Enable indexer**

```
sed -i -e 's|^indexer *=.*|indexer = "kv"|' $HOME/.mantrachain/config/config.toml
```

**UPDATE PRUNING**

```
sed -i \
  -e 's|^pruning *=.*|pruning = "custom"|' \
  -e 's|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|' \
  -e 's|^pruning-keep-every *=.*|pruning-keep-every = "0"|' \
  -e 's|^pruning-interval *=.*|pruning-interval = "19"|' \
  $HOME/.mantrachain/config/app.toml
```

### 🚨 Maintenance <a href="#maintenance" id="maintenance"></a>

**GET VALIDATOR INFO**

```
mantrachaind status 2>&1 | jq .ValidatorInfo
```

**GET SYNC INFO**

```
mantrachaind status 2>&1 | jq
```

**GET NODE PEER**

```
echo $(mantrachaind tendermint show-node-id)'@'$(curl -s ifconfig.me)':'$(cat $HOME/.mantrachain/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```

**CHECK IF VALIDATOR KEY IS CORRECT**

```
[[ $(mantrachaind q staking validator $(mantrachaind keys show wallet --bech val -a) -oj | jq -r .consensus_pubkey.key) = $(mantrachaind status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

**GET LIVE PEERS**

```
curl -sS http://localhost:14657/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}'
```

**SET MINIMUM GAS PRICE**

```
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"10000000000000mpx\"/" $HOME/.mantrachain/config/app.toml
```

**ENABLE PROMETHEUS**

```
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.mantrachain/config/config.toml
```

**RESET CHAIN DATA**

```
mantrachaind tendermint unsafe-reset-all --keep-addr-book --home $HOME/mantrachaind --keep-addr-book
```

**REMOVE NODE**

Please, before proceeding with the next step! All chain data will be lost! Make sure you have backed up your **priv\_validator\_key.json**!

```
cd $HOME
sudo systemctl stop mantrachaind
sudo systemctl disable mantrachaind
sudo rm /etc/systemd/system/mantrachaind
sudo systemctl daemon-reload
rm -f $(which mantrachaind)
rm -rf $HOME/.mantrachain
```

### ⚙️ Service Management <a href="#service-management" id="service-management"></a>

**RELOAD SERVICE CONFIGURATION**

```
sudo systemctl daemon-reload
```

**ENABLE SERVICE**

```
sudo systemctl enable mantrachaind
```

**DISABLE SERVICE**

```
sudo systemctl disable mantrachaind
```

**START SERVICE**

```
sudo systemctl start mantrachaind
```

**STOP SERVICE**

```
sudo systemctl stop mantrachaind
```

**RESTART SERVICE**

```
sudo systemctl restart mantrachaind
```

**CHECK SERVICE STATUS**

```
sudo systemctl status mantrachaind
```

**CHECK SERVICE LOGS**

```
sudo journalctl -u mantrachaind -f --no-hostname -o cat
```
