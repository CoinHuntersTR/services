# Useful commands

### 🔑 Key management <a href="#key-management" id="key-management"></a>

**ADD NEW KEY**

```
crossfid keys add wallet
```

**RECOVER EXISTING KEY**

```
crossfid keys add wallet --recover
```

**LIST ALL KEYS**

```
crossfid keys list
```

**DELETE KEY**

```
crossfid keys delete wallet
```

**EXPORT KEY TO A FILE**

```
crossfid keys export wallet
```

**IMPORT KEY FROM A FILE**

```
crossfid keys import wallet wallet.backup
```

**QUERY WALLET BALANCE**

```
crossfid q bank balances $(crossfid keys show wallet -a)
```

### 👷 Validator management <a href="#validator-management" id="validator-management"></a>

Please make sure you have adjusted **moniker**, **identity**, **details** and **website** to match your values.

**CREATE NEW VALIDATOR**

```
crossfid tx staking create-validator \
--amount 1000000mpx \
--from $WALLET \
--commission-rate 0.1 \
--commission-max-rate 0.2 \
--commission-max-change-rate 0.01 \
--min-self-delegation 1 \
--pubkey $(crossfid tendermint show-validator) \
--moniker "test" \
--identity "" \
--website "" \
--details "I love blockchain ❤️" \
--chain-id mineplex-mainnet-1 \
--gas auto --gas-adjustment 1.5 --gas-prices 10000000000000mpx \
-y
```

**EDIT EXISTING VALIDATOR**

```
crossfid tx staking edit-validator \
--new-moniker "YOUR_MONIKER_NAME" \
--identity "YOUR_KEYBASE_ID" \
--details "YOUR_DETAILS" \
--website "YOUR_WEBSITE_URL" \
--chain-id mineplex-mainnet-1 \
--commission-rate 0.05 \
--from wallet \
--gas-adjustment 1.4 \
--gas auto \
--gas-prices 10000000000000mpx \
-y
```

**UNJAIL VALIDATOR**

```
crossfid tx slashing unjail --from wallet --chain-id mineplex-mainnet-1 --gas-adjustment 1.4 --gas auto --gas-prices 10000000000000mpx -y
```

**JAIL REASON**

```
crossfid query slashing signing-info $(crossfid tendermint show-validator)
```

**LIST ALL ACTIVE VALIDATORS**

```
crossfid q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

**LIST ALL INACTIVE VALIDATORS**

```
crossfid q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_UNBONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

**VIEW VALIDATOR DETAILS**

```
crossfid q staking validator $(crossfid keys show wallet --bech val -a)
```

### 💲 Token management <a href="#token-management" id="token-management"></a>

**WITHDRAW REWARDS FROM ALL VALIDATORS**

```
crossfid tx distribution withdraw-all-rewards --from wallet --chain-id mineplex-mainnet-1 --gas-adjustment 1.4 --gas auto --gas-prices 10000000000000mpx -y
```

**WITHDRAW COMMISSION AND REWARDS FROM YOUR VALIDATOR**

```
crossfid tx distribution withdraw-rewards $(crossfid keys show wallet --bech val -a) --commission --from wallet --chain-id mineplex-mainnet-1 --gas-adjustment 1.4 --gas auto --gas-prices 10000000000000mpx -y
```

**DELEGATE TOKENS TO YOURSELF**

```
crossfid tx staking delegate $(crossfid keys show wallet --bech val -a) 1000000000000000mpx --from wallet --chain-id mineplex-mainnet-1 --gas-adjustment 1.4 --gas auto --gas-prices 10000000000000mpx -y
```

**DELEGATE TOKENS TO VALIDATOR**

```
crossfid tx staking delegate <TO_VALOPER_ADDRESS> 1000000000000000mpx --from wallet --chain-id mineplex-mainnet-1 --gas-adjustment 1.4 --gas auto --gas-prices 10000000000000mpx -y
```

**REDELEGATE TOKENS TO ANOTHER VALIDATOR**

```
crossfid tx staking redelegate $(crossfid keys show wallet --bech val -a) <TO_VALOPER_ADDRESS> 1000000000000000mpx --from wallet --chain-id mineplex-mainnet-1 --gas-adjustment 1.4 --gas auto --gas-prices 10000000000000mpx -y
```

**UNBOND TOKENS FROM YOUR VALIDATOR**

```
crossfid tx staking unbond $(crossfid keys show wallet --bech val -a) 1000000000000000mpx --from wallet --chain-id mineplex-mainnet-1 --gas-adjustment 1.4 --gas auto --gas-prices 10000000000000mpx -y
```

**SEND TOKENS TO THE WALLET**

```
crossfid tx bank send wallet <TO_WALLET_ADDRESS> 1000000000000000mpx --from wallet --chain-id mineplex-mainnet-1 --gas-adjustment 1.4 --gas auto --gas-prices 10000000000000mpx -y
```

### 🗳 Governance <a href="#governance" id="governance"></a>

**LIST ALL PROPOSALS**

```
crossfid query gov proposals
```

**VIEW PROPOSAL BY ID**

```
crossfid query gov proposal 1
```

**VOTE ‘YES’**

```
crossfid tx gov vote 1 yes --from wallet --chain-id mineplex-mainnet-1 --gas-adjustment 1.4 --gas auto --gas-prices 10000000000000mpx -y
```

**VOTE ‘NO’**

```
crossfid tx gov vote 1 no --from wallet --chain-id mineplex-mainnet-1 --gas-adjustment 1.4 --gas auto --gas-prices 10000000000000mpx -y
```

**VOTE ‘ABSTAIN’**

```
crossfid tx gov vote 1 abstain --from wallet --chain-id mineplex-mainnet-1 --gas-adjustment 1.4 --gas auto --gas-prices 10000000000000mpx -y
```

**VOTE ‘NOWITHVETO’**

```
crossfid tx gov vote 1 NoWithVeto --from wallet --chain-id mineplex-mainnet-1 --gas-adjustment 1.4 --gas auto --gas-prices 10000000000000mpx -y
```

### ⚡️ Utility <a href="#utility" id="utility"></a>

**UPDATE PORTS**

```
UPDATE PORTS
CUSTOM_PORT=110
sed -i -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${CUSTOM_PORT}58\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${CUSTOM_PORT}57\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${CUSTOM_PORT}60\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${CUSTOM_PORT}56\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${CUSTOM_PORT}66\"%" $HOME/.mineplex-chain/config/config.toml
sed -i -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:${CUSTOM_PORT}17\"%; s%^address = \":8080\"%address = \":${CUSTOM_PORT}80\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${CUSTOM_PORT}90\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${CUSTOM_PORT}91\"%" $HOME/.mineplex-chain/config/app.toml
```

**UPDATE INDEXER**

**Disable indexer**

```
sed -i -e 's|^indexer *=.*|indexer = "null"|' $HOME/.mineplex-chain/config/config.toml
```

**Enable indexer**

```
sed -i -e 's|^indexer *=.*|indexer = "kv"|' $HOME/.mineplex-chain/config/config.toml
```

**UPDATE PRUNING**

```
sed -i \
  -e 's|^pruning *=.*|pruning = "custom"|' \
  -e 's|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|' \
  -e 's|^pruning-keep-every *=.*|pruning-keep-every = "0"|' \
  -e 's|^pruning-interval *=.*|pruning-interval = "19"|' \
  $HOME/.mineplex-chain/config/app.toml
```

### 🚨 Maintenance <a href="#maintenance" id="maintenance"></a>

**GET VALIDATOR INFO**

```
crossfid status 2>&1 | jq .ValidatorInfo
```

**GET SYNC INFO**

```
crossfid status 2>&1 | jq .SyncInfo
```

**GET NODE PEER**

```
echo $(crossfid tendermint show-node-id)'@'$(curl -s ifconfig.me)':'$(cat $HOME/.mineplex-chain/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```

**CHECK IF VALIDATOR KEY IS CORRECT**

```
[[ $(crossfid q staking validator $(crossfid keys show wallet --bech val -a) -oj | jq -r .consensus_pubkey.key) = $(crossfid status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

**GET LIVE PEERS**

```
curl -sS http://localhost:14657/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}'
```

**SET MINIMUM GAS PRICE**

```
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"10000000000000mpx\"/" $HOME/.mineplex-chain/config/app.toml
```

**ENABLE PROMETHEUS**

```
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.mineplex-chain/config/config.toml
```

**RESET CHAIN DATA**

```
crossfid tendermint unsafe-reset-all --keep-addr-book --home $HOME/.mineplex-chain --keep-addr-book
```

**REMOVE NODE**

Please, before proceeding with the next step! All chain data will be lost! Make sure you have backed up your **priv\_validator\_key.json**!

```
cd $HOME
sudo systemctl stop crossfid
sudo systemctl disable crossfid
sudo rm /etc/systemd/system/crossfid
sudo systemctl daemon-reload
rm -f $(which crossfid)
rm -rf $HOME/.mineplex-chain
rm -rf $HOME/mineplex-chain
```

### ⚙️ Service Management <a href="#service-management" id="service-management"></a>

**RELOAD SERVICE CONFIGURATION**

```
sudo systemctl daemon-reload
```

**ENABLE SERVICE**

```
sudo systemctl enable crossfid
```

**DISABLE SERVICE**

```
sudo systemctl disable crossfid
```

**START SERVICE**

```
sudo systemctl start crossfid
```

**STOP SERVICE**

```
sudo systemctl stop crossfid
```

**RESTART SERVICE**

```
sudo systemctl restartcrossfid
```

**CHECK SERVICE STATUS**

```
sudo systemctl status crossfid
```

**CHECK SERVICE LOGS**

```
sudo journalctl -u crossfid -f --no-hostname -o cat
```
