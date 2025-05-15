# Useful commands

### 🔑 Key management <a href="#key-management" id="key-management"></a>

**ADD NEW KEY**

```
lumerad keys add wallet
```

**RECOVER EXISTING KEY**

```
lumerad keys add wallet --recover
```

**LIST ALL KEYS**

```
lumerad keys list
```

**DELETE KEY**

```
lumerad keys delete wallet
```

**EXPORT KEY TO A FILE**

```
lumerad keys export wallet
```

**IMPORT KEY FROM A FILE**

```
lumerad keys import wallet wallet.backup
```

**QUERY WALLET BALANCE**

```
lumerad q bank balances $(lumerad keys show wallet -a)
```

### 👷 Validator management <a href="#validator-management" id="validator-management"></a>

Please make sure you have adjusted **moniker**, **identity**, **details** and **website** to match your values.

**CREATE NEW VALIDATOR**

```
lumerad tx staking create-validator \
--amount 1000000ulume \
--from $WALLET \
--commission-rate 0.1 \
--commission-max-rate 0.2 \
--commission-max-change-rate 0.01 \
--min-self-delegation 1 \
--pubkey $(lumerad tendermint show-validator) \
--moniker "test" \
--identity "" \
--website "" \
--details "I love blockchain ❤️" \
--chain-id lumera-testnet-1 \
--gas auto --gas-adjustment 1.5 --gas-prices  0.025ulume \
-y
```

**EDIT EXISTING VALIDATOR**

```
lumerad tx staking edit-validator \
--new-moniker "YOUR_MONIKER_NAME" \
--identity "YOUR_KEYBASE_ID" \
--details "YOUR_DETAILS" \
--website "YOUR_WEBSITE_URL" \
--chain-id lumera-testnet-1 \
--commission-rate 0.05 \
--from wallet \
--gas-adjustment 1.5 \
--gas auto \
--gas-prices  0.025ulume \
-y
```

**UNJAIL VALIDATOR**

```
lumerad tx slashing unjail --from wallet --chain-id lumera-testnet-1 --gas-adjustment 1.5 --gas auto --gas-prices  0.025ulume -y
```

**JAIL REASON**

```
lumerad query slashing signing-info $(lumerad tendermint show-validator)
```

**LIST ALL ACTIVE VALIDATORS**

```
lumerad q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

**LIST ALL INACTIVE VALIDATORS**

```
lumerad q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_UNBONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

**VIEW VALIDATOR DETAILS**

```
lumerad q staking validator $(lumerad keys show wallet --bech val -a)
```

### 💲 Token management <a href="#token-management" id="token-management"></a>

**WITHDRAW REWARDS FROM ALL VALIDATORS**

```
lumerad tx distribution withdraw-all-rewards --from wallet --chain-id lumera-testnet-1 --gas-adjustment 1.5 --gas auto --gas-prices  0.025ulume -y
```

**WITHDRAW COMMISSION AND REWARDS FROM YOUR VALIDATOR**

```
lumerad tx distribution withdraw-rewards $(lumerad keys show wallet --bech val -a) --commission --from wallet --chain-id lumera-testnet-1 --gas-adjustment 1.5 --gas auto --gas-prices  0.025ulume -y
```

**DELEGATE TOKENS TO YOURSELF**

```
lumerad tx staking delegate $(lumerad keys show wallet --bech val -a) 1000000ulume --from wallet --chain-id lumera-testnet-1 --gas-adjustment 1.5 --gas auto --gas-prices  0.025ulume -y
```

**DELEGATE TOKENS TO VALIDATOR**

```
lumerad tx staking delegate <TO_VALOPER_ADDRESS> 1000000ulume --from wallet --chain-id lumera-testnet-1 --gas-adjustment 1.5 --gas auto --gas-prices  0.025ulume -y
```

**REDELEGATE TOKENS TO ANOTHER VALIDATOR**

```
lumerad tx staking redelegate $(lumerad keys show wallet --bech val -a) <TO_VALOPER_ADDRESS> 1000000ulume --from wallet --chain-id lumera-testnet-1 --gas-adjustment 1.5 --gas auto --gas-prices  0.025ulume -y
```

**UNBOND TOKENS FROM YOUR VALIDATOR**

```
lumerad tx staking unbond $(lumerad keys show wallet --bech val -a) 1000000ulume --from wallet --chain-id lumera-testnet-1 --gas-adjustment 1.5 --gas auto --gas-prices  0.025ulume -y
```

**SEND TOKENS TO THE WALLET**

```
lumerad tx bank send wallet <TO_WALLET_ADDRESS> 1000000ulume --from wallet --chain-id lumera-testnet-1 --gas-adjustment 1.5 --gas auto --gas-prices  0.025ulume -y
```

### 🗳 Governance <a href="#governance" id="governance"></a>

**LIST ALL PROPOSALS**

```
lumerad query gov proposals
```

**VIEW PROPOSAL BY ID**

```
lumerad query gov proposal 1
```

**VOTE ‘YES’**

```
lumerad tx gov vote 1 yes --from wallet --chain-id lumera-testnet-1 --gas-adjustment 1.5 --gas auto --gas-prices  0.025ulume -y
```

**VOTE ‘NO’**

```
lumerad tx gov vote 1 no --from wallet --chain-id lumera-testnet-1 --gas-adjustment 1.5 --gas auto --gas-prices  0.025ulume -y
```

**VOTE ‘ABSTAIN’**

```
lumerad tx gov vote 1 abstain --from wallet --chain-id lumera-testnet-1 --gas-adjustment 1.5 --gas auto --gas-prices  0.025ulume -y
```

**VOTE ‘NOWITHVETO’**

```
lumerad tx gov vote 1 NoWithVeto --from wallet --chain-id lumera-testnet-1 --gas-adjustment 1.5 --gas auto --gas-prices  0.025ulume -y
```

### ⚡️ Utility <a href="#utility" id="utility"></a>

**UPDATE PORTS**

```
UPDATE PORTS
CUSTOM_PORT=110
sed -i -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${CUSTOM_PORT}58\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${CUSTOM_PORT}57\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${CUSTOM_PORT}60\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${CUSTOM_PORT}56\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${CUSTOM_PORT}66\"%" $HOME/.lumera/config/config.toml
sed -i -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:${CUSTOM_PORT}17\"%; s%^address = \":8080\"%address = \":${CUSTOM_PORT}80\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${CUSTOM_PORT}90\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${CUSTOM_PORT}91\"%" $HOME/.lumera/config/app.toml
```

**UPDATE INDEXER**

**Disable indexer**

```
sed -i -e 's|^indexer *=.*|indexer = "null"|' $HOME/.lumera/config/config.toml
```

**Enable indexer**

```
sed -i -e 's|^indexer *=.*|indexer = "kv"|' $HOME/.lumera/config/config.toml
```

**UPDATE PRUNING**

```
sed -i \
  -e 's|^pruning *=.*|pruning = "custom"|' \
  -e 's|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|' \
  -e 's|^pruning-keep-every *=.*|pruning-keep-every = "0"|' \
  -e 's|^pruning-interval *=.*|pruning-interval = "19"|' \
  $HOME/.lumera/config/app.toml
```

### 🚨 Maintenance <a href="#maintenance" id="maintenance"></a>

**GET VALIDATOR INFO**

```
lumerad status 2>&1 | jq .ValidatorInfo
```

**GET SYNC INFO**

```
lumerad status 2>&1 | jq
```

**GET NODE PEER**

```
echo $(lumerad tendermint show-node-id)'@'$(curl -s ifconfig.me)':'$(cat $HOME/.lumera/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```

**CHECK IF VALIDATOR KEY IS CORRECT**

```
[[ $(lumerad q staking validator $(lumerad keys show wallet --bech val -a) -oj | jq -r .consensus_pubkey.key) = $(lumerad status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

**GET LIVE PEERS**

```
curl -sS http://localhost:14657/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}'
```

**SET MINIMUM GAS PRICE**

```
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"10000000000000mpx\"/" $HOME/.lumera/config/app.toml
```

**ENABLE PROMETHEUS**

```
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.lumera/config/config.toml
```

**RESET CHAIN DATA**

```
lumerad tendermint unsafe-reset-all --keep-addr-book --home $HOME/lumerad --keep-addr-book
```

**REMOVE NODE**

Please, before proceeding with the next step! All chain data will be lost! Make sure you have backed up your **priv\_validator\_key.json**!

```
cd $HOME
sudo systemctl stop lumerad
sudo systemctl disable lumerad
sudo rm /etc/systemd/system/lumerad
sudo systemctl daemon-reload
rm -f $(which lumerad)
rm -rf $HOME/.lumera
```

### ⚙️ Service Management <a href="#service-management" id="service-management"></a>

**RELOAD SERVICE CONFIGURATION**

```
sudo systemctl daemon-reload
```

**ENABLE SERVICE**

```
sudo systemctl enable lumerad
```

**DISABLE SERVICE**

```
sudo systemctl disable lumerad
```

**START SERVICE**

```
sudo systemctl start lumerad
```

**STOP SERVICE**

```
sudo systemctl stop lumerad
```

**RESTART SERVICE**

```
sudo systemctl restart lumerad
```

**CHECK SERVICE STATUS**

```
sudo systemctl status lumerad
```

**CHECK SERVICE LOGS**

```
sudo journalctl -u lumerad -f --no-hostname -o cat
```
