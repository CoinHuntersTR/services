# Useful commands

### 🔑 Key management <a href="#key-management" id="key-management"></a>

**ADD NEW KEY**

```
0gchaind keys add wallet --eth
```

**RECOVER EXISTING KEY**

```
0gchaind keys add wallet --recover --eth
```

**LIST ALL KEYS**

```
0gchaind keys list
```

**DELETE KEY**

```
0gchaind keys delete wallet
```

**EXPORT KEY TO A FILE**

```
0gchaind keys export wallet
```

**IMPORT KEY FROM A FILE**

```
0gchaind keys import wallet wallet.backup
```

**QUERY WALLET BALANCE**

```
0gchaind q bank balances $(0gchaind keys show wallet -a)
```

### 👷 Validator management <a href="#validator-management" id="validator-management"></a>

Please make sure you have adjusted **moniker**, **identity**, **details** and **website** to match your values.

**CREATE NEW VALIDATOR**

```
0gchaind tx staking create-validator \
--amount 100000ua0gi \
--pubkey $(0gchaind tendermint show-validator) \
--moniker "YOUR_MONIKER_NAME" \
--identity "YOUR_KEYBASE_ID" \
--details "YOUR_DETAILS" \
--website "YOUR_WEBSITE_URL" \
--chain-id zgtendermint_16600-1 \
--commission-rate 0.05 \
--commission-max-rate 0.20 \
--commission-max-change-rate 0.05 \
--min-self-delegation 1 \
--from wallet \
--gas-adjustment 1.5 \
--gas auto \
--gas-prices 9999ua0gi \
-y
```

**EDIT EXISTING VALIDATOR**

```
0gchaind tx staking edit-validator \
--new-moniker "YOUR_MONIKER_NAME" \
--identity "YOUR_KEYBASE_ID" \
--details "YOUR_DETAILS" \
--website "YOUR_WEBSITE_URL" \
--chain-id zgtendermint_16600-1 \
--commission-rate 0.05 \
--from wallet \
--gas-adjustment 1.5 \
--gas auto \
--gas-prices 9999ua0gi \
-y
```

**UNJAIL VALIDATOR**

```
0gchaind tx slashing unjail --from wallet --chain-id zgtendermint_16600-1 --gas-adjustment 1.5 --gas auto --gas-prices 9999ua0gi -y
```

**JAIL REASON**

```
0gchaind query slashing signing-info $(0gchaind tendermint show-validator)
```

**LIST ALL ACTIVE VALIDATORS**

```
0gchaind q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

**LIST ALL INACTIVE VALIDATORS**

```
0gchaind q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_UNBONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

**VIEW VALIDATOR DETAILS**

```
0gchaind q staking validator $(0gchaind keys show wallet --bech val -a)
```

### 💲 Token management <a href="#token-management" id="token-management"></a>

**WITHDRAW REWARDS FROM ALL VALIDATORS**

```
0gchaind tx distribution withdraw-all-rewards --from wallet --chain-id zgtendermint_16600-1 --gas-adjustment 1.5 --gas auto --gas-prices 9999ua0gi -y
```

**WITHDRAW COMMISSION AND REWARDS FROM YOUR VALIDATOR**

```
0gchaind tx distribution withdraw-rewards $(0gchaind keys show wallet --bech val -a) --commission --from wallet --chain-id zgtendermint_16600-1 --gas-adjustment 1.5 --gas auto --gas-prices 9999ua0gi -y
```

**DELEGATE TOKENS TO YOURSELF**

```
0gchaind tx staking delegate $(0gchaind keys show wallet --bech val -a) 100000ua0gi --from wallet --chain-id zgtendermint_16600-1 --gas-adjustment 1.5 --gas auto --gas-prices 9999ua0gi -y
```

**DELEGATE TOKENS TO VALIDATOR**

```
0gchaind tx staking delegate <TO_VALOPER_ADDRESS> 100000ua0gi --from wallet --chain-id zgtendermint_16600-1 --gas-adjustment 1.5 --gas auto --gas-prices 9999ua0gi -y
```

**REDELEGATE TOKENS TO ANOTHER VALIDATOR**

```
0gchaind tx staking redelegate $(0gchaind keys show wallet --bech val -a) <TO_VALOPER_ADDRESS> 100000ua0gi --from wallet --chain-id zgtendermint_16600-1 --gas-adjustment 1.5 --gas auto --gas-prices 9999ua0gi -y
```

**UNBOND TOKENS FROM YOUR VALIDATOR**

```
0gchaind tx staking unbond $(0gchaind keys show wallet --bech val -a) 100000ua0gi --from wallet --chain-id zgtendermint_16600-1 --gas-adjustment 1.5 --gas auto --gas-prices 9999ua0gi -y
```

**SEND TOKENS TO THE WALLET**

```
0gchaind tx bank send wallet <TO_WALLET_ADDRESS> 100000ua0gi --from wallet --chain-id zgtendermint_16600-1 --gas-adjustment 1.5 --gas auto --gas-prices 9999ua0gi -y
```

### 🗳 Governance <a href="#governance" id="governance"></a>

**LIST ALL PROPOSALS**

```
0gchaind query gov proposals
```

**VIEW PROPOSAL BY ID**

```
0gchaind query gov proposal 1
```

**VOTE ‘YES’**

```
0gchaind tx gov vote 1 yes --from wallet --chain-id zgtendermint_16600-1 --gas-adjustment 1.5 --gas auto --gas-prices 9999ua0gi -y
```

**VOTE ‘NO’**

```
0gchaind tx gov vote 1 no --from wallet --chain-id zgtendermint_16600-1 --gas-adjustment 1.5 --gas auto --gas-prices 9999ua0gi -y
```

**VOTE ‘ABSTAIN’**

```
0gchaind tx gov vote 1 abstain --from wallet --chain-id zgtendermint_16600-1 --gas-adjustment 1.5 --gas auto --gas-prices 9999ua0gi -y
```

**VOTE ‘NOWITHVETO’**

```
0gchaind tx gov vote 1 NoWithVeto --from wallet --chain-id zgtendermint_16600-1 --gas-adjustment 1.5 --gas auto --gas-prices 9999ua0gi -y
```

### ⚡️ Utility <a href="#utility" id="utility"></a>

**UPDATE PORTS**

```
CUSTOM_PORT=110
sed -i -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${CUSTOM_PORT}58\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${CUSTOM_PORT}57\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${CUSTOM_PORT}60\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${CUSTOM_PORT}56\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${CUSTOM_PORT}66\"%" $HOME/.0gchaind/config/config.toml
sed -i -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:${CUSTOM_PORT}17\"%; s%^address = \":8080\"%address = \":${CUSTOM_PORT}80\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${CUSTOM_PORT}90\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${CUSTOM_PORT}91\"%" $HOME/.0gchaind/config/app.toml
```

**UPDATE INDEXER**

**Disable indexer**

```
sed -i -e 's|^indexer *=.*|indexer = "null"|' $HOME/.0gchaind/config/config.toml
```

**Enable indexer**

```
sed -i -e 's|^indexer *=.*|indexer = "kv"|' $HOME/.0gchaind/config/config.toml
```

**UPDATE PRUNING**

```
sed -i \
  -e 's|^pruning *=.*|pruning = "custom"|' \
  -e 's|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|' \
  -e 's|^pruning-keep-every *=.*|pruning-keep-every = "0"|' \
  -e 's|^pruning-interval *=.*|pruning-interval = "19"|' \
  $HOME/.0gchaind/config/app.toml
```

### 🚨 Maintenance <a href="#maintenance" id="maintenance"></a>

**GET VALIDATOR INFO**

```
0gchaind status 2>&1 | jq .ValidatorInfo
```

**GET SYNC INFO**

```
0gchaind status 2>&1 | jq .SyncInfo
```

**GET NODE PEER**

```
echo $(0gchaind tendermint show-node-id)'@'$(curl -s ifconfig.me)':'$(cat $HOME/.0gchaind/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```

**CHECK IF VALIDATOR KEY IS CORRECT**

```
[[ $(0gchaind q staking validator $(0gchaind keys show wallet --bech val -a) -oj | jq -r .consensus_pubkey.key) = $(0gchaind status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

**GET LIVE PEERS**

```
curl -sS http://localhost:14657/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}'
```

**SET MINIMUM GAS PRICE**

```
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"20000000000udym\"/" $HOME/.0gchaind/config/app.toml
```

**ENABLE PROMETHEUS**

```
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.0gchaind/config/config.toml
```

**RESET CHAIN DATA**

```
0gchaind tendermint unsafe-reset-all --keep-addr-book --home $HOME/.0gchaind --keep-addr-book
```

**REMOVE NODE**

Please, before proceeding with the next step! All chain data will be lost! Make sure you have backed up your **priv\_validator\_key.json**!

```
cd $HOME
sudo systemctl stop 0gchain
sudo systemctl disable 0gchain
sudo rm /etc/systemd/system/0gchain
sudo systemctl daemon-reload
rm -f $(which 0gchaind)
rm -rf $HOME/.0gchaind
rm -rf $HOME/0gchaind
```

### ⚙️ Service Management <a href="#service-management" id="service-management"></a>

**RELOAD SERVICE CONFIGURATION**

```
sudo systemctl daemon-reload
```

**ENABLE SERVICE**

```
sudo systemctl enable 0gchain
```

**DISABLE SERVICE**

```
sudo systemctl disable 0gchain
```

**START SERVICE**

```
sudo systemctl start 0gchain
```

**STOP SERVICE**

```
sudo systemctl stop 0gchain
```

**RESTART SERVICE**

```
sudo systemctl restart 0gchain
```

**CHECK SERVICE STATUS**

```
sudo systemctl status 0gchain
```

**CHECK SERVICE LOGS**

```
sudo journalctl -u 0gchain -f --no-hostname -o cat
```
