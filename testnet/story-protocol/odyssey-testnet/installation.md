---
description: odyssey testnet
---

# Installation

#### System Requirements

<table><thead><tr><th width="210">Hardware</th><th>Minimum Requirement</th></tr></thead><tbody><tr><td>CPU</td><td>4 Cores</td></tr><tr><td>RAM</td><td> 8 GB</td></tr><tr><td>DISK</td><td>500 SSD</td></tr></tbody></table>

#### Auto Install

While setting up Story Protocol, you will be asked for your **Moniker** name and the **port number** you want to use. After entering this information, you can successfully set up the Story Protocol odyssey testnet network.

```bash
bash <(wget -qO- https://raw.githubusercontent.com/CoinHuntersTR/props/refs/heads/main/AutoInstall/story-odyssey.sh)
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

Import the private key of your validator wallet into your MetaMask wallet. Then, use the faucet to request IP test tokens.

Faucet : [ https://faucet.story.foundation/](https://faucet.story.foundation/)

#### Register validator

```
story validator create --stake 1000000000000000000 --private-key $(cat $HOME/.story/story/config/private_
```



### Move the wallet and validator in the illiad test network.

Firstly, on your server in illiad test network; **/root/.story/story/story/config/** go to this folder. In the folder;&#x20;

**1- ‘/root/.story/story/story/config/private\_key.txt’**

**2- ‘/root/.story/story/story/config/priv\_validator\_key.json’**&#x20;

**3-‘/root/.story/story/story/config/node\_key.json’**

back up the files. When the server we are working for odyssey  matches the network, we find the same folder path and import the files I mentioned above. Then we restart with story and story-geth restart. We have our validator address and wallet from the previous network.
