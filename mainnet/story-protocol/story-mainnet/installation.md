# Installation

#### System Requirements

Linux 22.04 arm64

<table><thead><tr><th width="241">Hardware</th><th>Minimum Requirement</th></tr></thead><tbody><tr><td>CPU</td><td>8 Cores</td></tr><tr><td>RAM</td><td>32 GB</td></tr><tr><td>DISK</td><td>500 SSD</td></tr></tbody></table>

#### Offical Doc: [https://docs.story.foundation/docs/node-setup-mainnet](https://docs.story.foundation/docs/node-setup-mainnet)

#### Auto Install

While setting up Story Protocol, you will be asked for your **Moniker** name and the **port number** you want to use. After entering this information, you can successfully set up the Story Protocol  Devoloper Mainnet network.

```bash
bash <(wget -qO- https://raw.githubusercontent.com/CoinHuntersTR/props/refs/heads/main/AutoInstall/story.sh)
```

#### Check Logs for story-geth and story

```
sudo journalctl -u story-geth -f -o cat
sudo journalctl -u story -f -o cat
```

#### Synchronization Check

To check if your synchronization is complete, you can enter the following command. When entering the command, don't forget to replace '\<portnumber>' with the port number you provided during setup. If you receive a 'False' output, you can proceed with the next steps.

```
curl localhost:<portnumber>657/status | jq
```

#### Export Wallet

```
story validator export --export-evm-key
```

Get wallet key and import to Metamask wallet&#x20;

```
sudo nano ~/.story/story/config/private_key.txt
```

Import the private key of your validator wallet into your MetaMask wallet. min delegate 1024 IP

#### Register validator

```
story validator create \
--stake 1024000000000000000000 \
--moniker "" \
--chain-id 1514 \
--private-key $(cat $HOME/.story/story/config/private_key.txt | grep "PRIVATE_KEY" | awk -F'=' '{print $2}') \
--commission-rate 800 \
--max-commission-rate 5000 \
--max-commission-change-rate 500 \
--unlocked=false
```

