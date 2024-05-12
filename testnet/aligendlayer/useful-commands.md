# Useful commands

### 🔑 Key management <a href="#key-management" id="key-management"></a>

**ADD NEW KEY**

```
alignedlayerd keys add wallet
```

**RECOVER EXISTING KEY**

```
alignedlayerd keys add wallet --recover
```

**LIST ALL KEYS**

```
alignedlayerd keys list
```

**DELETE KEY**

```
alignedlayerd keys delete wallet
```

**EXPORT KEY TO A FILE**

```
alignedlayerd keys export wallet
```

**IMPORT KEY FROM A FILE**

```
alignedlayerd keys import wallet wallet.backup
```

**QUERY WALLET BALANCE**

```
alignedlayerd q bank balances $(alignedlayerd keys show wallet -a)
```

### 👷 Validator management <a href="#validator-management" id="validator-management"></a>

To create validator please refer to [official documentation](https://union.build/docs/joining-testnet/creating-validators/)

**UNJAIL VALIDATOR**

```
alignedlayerd tx slashing unjail --from wallet --chain-id alignedlayer --gas-adjustment 1.4 --gas auto --gas-prices 0stake -y
```

**JAIL REASON**

```
alignedlayerd query slashing signing-info $(alignedlayerd tendermint show-validator)
```

**LIST ALL ACTIVE VALIDATORS**

```
alignedlayerd q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

**LIST ALL INACTIVE VALIDATORS**

```
alignedlayerd q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_UNBONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

**VIEW VALIDATOR DETAILS**

```
alignedlayerd q staking validator $(alignedlayerd keys show wallet --bech val -a)
```

### 💲 Token management <a href="#token-management" id="token-management"></a>

**WITHDRAW REWARDS FROM ALL VALIDATORS**

```
alignedlayerd tx distribution withdraw-all-rewards --from wallet --chain-id alignedlayer --gas-adjustment 1.4 --gas auto --gas-prices 0stake -y
```

**WITHDRAW COMMISSION AND REWARDS FROM YOUR VALIDATOR**

```
alignedlayerd tx distribution withdraw-rewards $(alignedlayerd keys show wallet --bech val -a) --commission --from wallet --chain-id alignedlayer --gas-adjustment 1.4 --gas auto --gas-prices 0stake -y
```

**DELEGATE TOKENS TO YOURSELF**

```
alignedlayerd tx staking delegate $(alignedlayerd keys show wallet --bech val -a) 1000000stake --from wallet --chain-id alignedlayer --gas-adjustment 1.4 --gas auto --gas-prices 0stake -y
```

**DELEGATE TOKENS TO VALIDATOR**

```
alignedlayerd tx staking delegate <TO_VALOPER_ADDRESS> 1000000stake --from wallet --chain-id alignedlayer --gas-adjustment 1.4 --gas auto --gas-prices 0stake -y
```

**REDELEGATE TOKENS TO ANOTHER VALIDATOR**

```
alignedlayerd tx staking redelegate $(alignedlayerd keys show wallet --bech val -a) <TO_VALOPER_ADDRESS> 1000000stake --from wallet --chain-id alignedlayer --gas-adjustment 1.4 --gas auto --gas-prices 0stake -y
```

**UNBOND TOKENS FROM YOUR VALIDATOR**

```
alignedlayerd tx staking unbond $(alignedlayerd keys show wallet --bech val -a) 1000000stake --from wallet --chain-id alignedlayer --gas-adjustment 1.4 --gas auto --gas-prices 0stake -y
```

**SEND TOKENS TO THE WALLET**

```
alignedlayerd tx bank send wallet <TO_WALLET_ADDRESS> 1000000stake --from wallet --chain-id alignedlayer --gas-adjustment 1.4 --gas auto --gas-prices 0stake -y
```

### 🗳 Governance <a href="#governance" id="governance"></a>

**LIST ALL PROPOSALS**

```
alignedlayerd query gov proposals
```

**VIEW PROPOSAL BY ID**

```
alignedlayerd query gov proposal 1
```

**VOTE ‘YES’**

```
alignedlayerd tx gov vote 1 yes --from wallet --chain-id alignedlayer --gas-adjustment 1.4 --gas auto --gas-prices 0stake -y
```

**VOTE ‘NO’**

```
alignedlayerd tx gov vote 1 no --from wallet --chain-id alignedlayer --gas-adjustment 1.4 --gas auto --gas-prices 0stake -y
```

**VOTE ‘ABSTAIN’**

```
alignedlayerd tx gov vote 1 abstain --from wallet --chain-id alignedlayer --gas-adjustment 1.4 --gas auto --gas-prices 0stake -y
```

**VOTE ‘NOWITHVETO’**

```
alignedlayerd tx gov vote 1 NoWithVeto --from wallet --chain-id alignedlayer --gas-adjustment 1.4 --gas auto --gas-prices 0stake -y
```

### ⚡️ Utility <a href="#utility" id="utility"></a>

**UPDATE PORTS**

```
CUSTOM_PORT=110
sed -i -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${CUSTOM_PORT}58\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${CUSTOM_PORT}57\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${CUSTOM_PORT}60\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${CUSTOM_PORT}56\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${CUSTOM_PORT}66\"%" $HOME/.alignedlayer/config/config.toml
sed -i -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:${CUSTOM_PORT}17\"%; s%^address = \":8080\"%address = \":${CUSTOM_PORT}80\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${CUSTOM_PORT}90\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${CUSTOM_PORT}91\"%" $HOME/.alignedlayer/config/app.toml
```

**UPDATE INDEXER**

**Disable indexer**

```
sed -i -e 's|^indexer *=.*|indexer = "null"|' $HOME/.alignedlayer/config/config.toml
```

**Enable indexer**

```
sed -i -e 's|^indexer *=.*|indexer = "kv"|' $HOME/.alignedlayer/config/config.toml
```

**UPDATE PRUNING**

```
sed -i \
  -e 's|^pruning *=.*|pruning = "custom"|' \
  -e 's|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|' \
  -e 's|^pruning-keep-every *=.*|pruning-keep-every = "0"|' \
  -e 's|^pruning-interval *=.*|pruning-interval = "19"|' \
  $HOME/.alignedlayer/config/app.toml
```

### 🚨 Maintenance <a href="#maintenance" id="maintenance"></a>

**GET VALIDATOR INFO**

```
alignedlayerd status 2>&1 | jq .ValidatorInfo
```

**GET SYNC INFO**

```
alignedlayerd status 2>&1 | jq .SyncInfo
```

**GET NODE PEER**

```
echo $(alignedlayerd tendermint show-node-id)'@'$(curl -s ifconfig.me)':'$(cat $HOME/.alignedlayer/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```

**CHECK IF VALIDATOR KEY IS CORRECT**

```
[[ $(alignedlayerd q staking validator $(alignedlayerd keys show wallet --bech val -a) -oj | jq -r .consensus_pubkey.key) = $(alignedlayerd status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

**GET LIVE PEERS**

```
curl -sS http://localhost:17157/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}'
```

**SET MINIMUM GAS PRICE**

```
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0stake\"/" $HOME/.alignedlayer/config/app.toml
```

**ENABLE PROMETHEUS**

```
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.alignedlayer/config/config.toml
```

**RESET CHAIN DATA**

```
alignedlayerd tendermint unsafe-reset-all --keep-addr-book --home $HOME/.alignedlayer --keep-addr-book
```

**REMOVE NODE**

Please, before proceeding with the next step! All chain data will be lost! Make sure you have backed up your **priv\_validator\_key.json**!

```
cd $HOME
sudo systemctl stop alignedlayerd.service
sudo systemctl disable alignedlayerd.service
sudo rm /etc/systemd/system/alignedlayerd.service
sudo systemctl daemon-reload
rm -f $(which alignedlayerd)
rm -rf $HOME/.alignedlayer
```

### ⚙️ Service Management <a href="#service-management" id="service-management"></a>

**RELOAD SERVICE CONFIGURATION**

```
sudo systemctl daemon-reload
```

**ENABLE SERVICE**

```
sudo systemctl enable alignedlayerd.service
```

**DISABLE SERVICE**

```
sudo systemctl disable alignedlayerd.service
```

**START SERVICE**

```
sudo systemctl start alignedlayerd.service
```

**STOP SERVICE**

```
sudo systemctl stop alignedlayerd.service
```

**RESTART SERVICE**

```
sudo systemctl restart alignedlayerd.service
```

**CHECK SERVICE STATUS**

```
sudo systemctl status alignedlayerd.service
```

**CHECK SERVICE LOGS**

```
sudo journalctl -u alignedlayerd.service -f --no-hostname -o cat
```
