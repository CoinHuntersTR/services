# Useful commands

### 🔑 Key management <a href="#key-management" id="key-management"></a>

**ADD NEW KEY**

```
sided keys add wallet --key-type="taproot"
```

**RECOVER EXISTING KEY**

```
sided keys add wallet --recover --key-type="taproot"
```

**LIST ALL KEYS**

```
sided keys list
```

**DELETE KEY**

```
sided keys delete wallet
```

**EXPORT KEY TO A FILE**

```
sided keys export wallet
```

**IMPORT KEY FROM A FILE**

```
sided keys import wallet wallet.backup
```

**QUERY WALLET BALANCE**

```
sided q bank balances $(sided keys show wallet -a)
```

### 👷 Validator management <a href="#validator-management" id="validator-management"></a>

Please make sure you have adjusted **moniker**, **identity**, **details** and **website** to match your values.

**CREATE NEW VALIDATOR**

```
cat << EOF > ~/.side/config/validator.json
{
	"pubkey": {"@type":"/cosmos.crypto.ed25519.PubKey","key":"BCDw/cq+7VxwNU/UIDc08JaYXru0Wa8SPoamzYSHfY8="},
	"amount": "1000000uside",
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
```
sided tx staking create-validator ~/.side/config/validator.json --from wallet --chain-id sidechain-1 --fees 100000uside
```

**UNJAIL VALIDATOR**

```
sided tx slashing unjail --from wallet --chain-id sidechain-1 --gas-adjustment 1.4 --gas auto --gas-prices 100000uside -y
```

**JAIL REASON**

```
sided query slashing signing-info $(sided tendermint show-validator)
```

**LIST ALL ACTIVE VALIDATORS**

```
sided q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

**LIST ALL INACTIVE VALIDATORS**

```
sided q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_UNBONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

**VIEW VALIDATOR DETAILS**

```
sided q staking validator $(sided keys show wallet --bech val -a)
```

### 💲 Token management <a href="#token-management" id="token-management"></a>

**WITHDRAW REWARDS FROM ALL VALIDATORS**

```
sided tx distribution withdraw-all-rewards --from wallet --chain-id sidechain-1 --gas-adjustment 1.4 --gas auto --gas-prices 100000uside -y
```

**WITHDRAW COMMISSION AND REWARDS FROM YOUR VALIDATOR**

```
sided tx distribution withdraw-rewards $(sided keys show wallet --bech val -a) --commission --from wallet --chain-id sidechain-1 --gas-adjustment 1.4 --gas auto --gas-prices 100000uside -y
```

**DELEGATE TOKENS TO YOURSELF**

```
sided tx staking delegate $(sided keys show wallet --bech val -a) 1000000uside --from wallet --chain-id sidechain-1 --gas-adjustment 1.4 --gas auto --gas-prices 100000uside -y
```

**DELEGATE TOKENS TO VALIDATOR**

```
sided tx staking delegate <TO_VALOPER_ADDRESS> 1000000uside --from wallet --chain-id sidechain-1 --gas-adjustment 1.4 --gas auto --gas-prices 100000uside -y
```

**REDELEGATE TOKENS TO ANOTHER VALIDATOR**

```
sided tx staking redelegate $(sided keys show wallet --bech val -a) <TO_VALOPER_ADDRESS> 1000000uside --from wallet --chain-id sidechain-1 --gas-adjustment 1.4 --gas auto --gas-prices 100000uside -y
```

**UNBOND TOKENS FROM YOUR VALIDATOR**

```
sided tx staking unbond $(sided keys show wallet --bech val -a) 1000000uside --from wallet --chain-id sidechain-1 --gas-adjustment 1.4 --gas auto --gas-prices 100000uside -y
```

**SEND TOKENS TO THE WALLET**

```
sided tx bank send wallet <TO_WALLET_ADDRESS> 1000000uside --from wallet --chain-id sidechain-1 --gas-adjustment 1.4 --gas auto --gas-prices 100000uside -y
```

### 🗳 Governance <a href="#governance" id="governance"></a>

**LIST ALL PROPOSALS**

```
sided query gov proposals
```

**VIEW PROPOSAL BY ID**

```
sided query gov proposal 1
```

**VOTE ‘YES’**

```
sided tx gov vote 1 yes --from wallet --chain-id sidechain-1 --gas-adjustment 1.4 --gas auto --gas-prices 100000uside -y
```

**VOTE ‘NO’**

```
sided tx gov vote 1 no --from wallet --chain-id sidechain-1 --gas-adjustment 1.4 --gas auto --gas-prices 100000uside -y
```

**VOTE ‘ABSTAIN’**

```
sided tx gov vote 1 abstain --from wallet --chain-id sidechain-1 --gas-adjustment 1.4 --gas auto --gas-prices 100000uside -y
```

**VOTE ‘NOWITHVETO’**

```
sided tx gov vote 1 NoWithVeto --from wallet --chain-id sidechain-1 --gas-adjustment 1.4 --gas auto --gas-prices 100000uside -y
```

### ⚡️ Utility <a href="#utility" id="utility"></a>

**UPDATE PORTS**

```
CUSTOM_PORT=110
sed -i -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${CUSTOM_PORT}58\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${CUSTOM_PORT}57\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${CUSTOM_PORT}60\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${CUSTOM_PORT}56\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${CUSTOM_PORT}66\"%" $HOME/.side/config/config.toml
sed -i -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:${CUSTOM_PORT}17\"%; s%^address = \":8080\"%address = \":${CUSTOM_PORT}80\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${CUSTOM_PORT}90\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${CUSTOM_PORT}91\"%" $HOME/.side/config/app.toml
```

**UPDATE INDEXER**

**Disable indexer**

```
sed -i -e 's|^indexer *=.*|indexer = "null"|' $HOME/.side/config/config.toml
```

**Enable indexer**

```
sed -i -e 's|^indexer *=.*|indexer = "kv"|' $HOME/.side/config/config.toml
```

**UPDATE PRUNING**

```
sed -i \
  -e 's|^pruning *=.*|pruning = "custom"|' \
  -e 's|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|' \
  -e 's|^pruning-keep-every *=.*|pruning-keep-every = "0"|' \
  -e 's|^pruning-interval *=.*|pruning-interval = "19"|' \
  $HOME/.side/config/app.toml
```

### 🚨 Maintenance <a href="#maintenance" id="maintenance"></a>

**GET VALIDATOR INFO**

```
sided status 2>&1 | jq .ValidatorInfo
```

**GET SYNC INFO**

```
sided status 2>&1 | jq .SyncInfo
```

**GET NODE PEER**

```
echo $(sided tendermint show-node-id)'@'$(curl -s ifconfig.me)':'$(cat $HOME/.side/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```

**CHECK IF VALIDATOR KEY IS CORRECT**

```
[[ $(sided q staking validator $(sided keys show wallet --bech val -a) -oj | jq -r .consensus_pubkey.key) = $(sided status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

**GET LIVE PEERS**

```
curl -sS http://localhost:14457/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}'
```

**SET MINIMUM GAS PRICE**

```
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"100000uside\"/" $HOME/.side/config/app.toml
```

**ENABLE PROMETHEUS**

```
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.side/config/config.toml
```

**RESET CHAIN DATA**

```
sided tendermint unsafe-reset-all --keep-addr-book --home $HOME/.side --keep-addr-book
```

**REMOVE NODE**

Please, before proceeding with the next step! All chain data will be lost! Make sure you have backed up your **priv\_validator\_key.json**!

```
cd $HOME
sudo systemctl stop sided
sudo systemctl disable sided
sudo rm /etc/systemd/system/sided.service
sudo systemctl daemon-reload
rm -f $(which sided)
rm -rf $HOME/.side
rm -rf $HOME/side
```

### ⚙️ Service Management <a href="#service-management" id="service-management"></a>

**RELOAD SERVICE CONFIGURATION**

```
sudo systemctl daemon-reload
```

**ENABLE SERVICE**

```
sudo systemctl enable sided
```

**DISABLE SERVICE**

```
sudo systemctl disable sided
```

**START SERVICE**

```
sudo systemctl start sided
```

**STOP SERVICE**

```
sudo systemctl stop sided
```

**RESTART SERVICE**

```
sudo systemctl restart sided
```

**CHECK SERVICE STATUS**

```
sudo systemctl status sided
```

**CHECK SERVICE LOGS**

```
sudo journalctl -u sided -f --no-hostname -o cat
```
