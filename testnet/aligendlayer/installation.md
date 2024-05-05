# Installation

### 1. Install GOLANG:[​](https://services.staketab.org/docs/aligned-testnet/auto/#1-install-golang) <a href="#id-1-install-golang" id="id-1-install-golang"></a>

Install custom version of Golang #GO.\
Or you can install GO from [official website](https://golang.org/doc/install).

Specify version and GO path in this line `./go.sh -v GO_VERSION -p GO_PATH`\
Example `./go.sh -v 1.19.4 -p /root/go`

#### You can use all the variables or not use them at all and then the GO\_VERSION and GO\_PATH will be used by default as (-v 1.19.4 -p /usr/local/go)[​](https://services.staketab.org/docs/aligned-testnet/auto/#you-can-use-all-the-variables-or-not-use-them-at-all-and-then-the-go\_version-and-go\_path-will-be-used-by-default-as--v-1194--p-usrlocalgo) <a href="#you-can-use-all-the-variables-or-not-use-them-at-all-and-then-the-go_version-and-go_path-will-be-use" id="you-can-use-all-the-variables-or-not-use-them-at-all-and-then-the-go_version-and-go_path-will-be-use"></a>

```
wget https://raw.githubusercontent.com/Staketab/node-tools/main/components/golang/go.sh
chmod +x go.sh && ./go.sh -v 1.22.0
rm -rf go.sh
```

Now apply the changes with the command below or reboot your terminal.

```
. /etc/profile && . $HOME/.bashrc
```

### 2. Run Node setup:[​](https://services.staketab.org/docs/aligned-testnet/auto/#2-run-node-setup) <a href="#id-2-run-node-setup" id="id-2-run-node-setup"></a>

The run command should look like this:

```
wget https://raw.githubusercontent.com/Staketab/cosmos-tools/main/node-installer/install.sh
chmod +x install.sh
./install.sh -g yetanotherco -f aligned_layer_tendermint -b alignedlayerd -c .alignedlayer -v v0.0.3
rm -rf install.sh && . $HOME/.profile
```

### 3. Data for start the chain.[​](https://services.staketab.org/docs/aligned-testnet/auto/#3-data-for-start-the-chain) <a href="#id-3-data-for-start-the-chain" id="id-3-data-for-start-the-chain"></a>

* Binary link:

```
https://services.staketab.org/aligned-testnet/alignedlayerd
```

* Chain-id:

```
alignedlayer
```

* Genesis file:

```
https://services.staketab.org/aligned-testnet/genesis.json
```

* Peers:

```
81138177a67195791bbe782fe1ed49f25e582bac@91.107.239.79:26656,c5d0498e345725365c1016795eecff4a67e4c4c9@88.99.174.203:26656,14af04afc663427604e8dd53f4023f7963a255cb@116.203.81.174:26656,9c89e77d51561c8b23957eee85a81ccc99fa7d6b@128.140.3.188:26656
```

*   Seed:

    `None`
* minimum-gas-prices:

```
0stake
```

### 4. Service commands.[​](https://services.staketab.org/docs/aligned-testnet/auto/#4-service-commands) <a href="#id-4-service-commands" id="id-4-service-commands"></a>

* Restart service

```
sudo systemctl restart alignedlayerd.service
```

* Service logs

```
sudo journalctl -u alignedlayerd.service -f
```

* Stop service

```
sudo systemctl stop alignedlayerd.service
```

* Reload configuration change

```
systemctl daemon-reload
```

### 5. SnapShot

#### Stop the service:[​](https://services.staketab.org/docs/aligned-testnet/snapshot/#stop-the-service) <a href="#stop-the-service" id="stop-the-service"></a>

Replace the service to your service name.

```
sudo systemctl stop alignedlayerd.service
```

#### Backup priv\_validator\_state.json and remove old data:[​](https://services.staketab.org/docs/aligned-testnet/snapshot/#backup-priv\_validator\_statejson-and-remove-old-data) <a href="#backup-priv_validator_statejson-and-remove-old-data" id="backup-priv_validator_statejson-and-remove-old-data"></a>

To prevent double-signing a block and tombstone your validator:

* Backup priv\_validator\_state.json:

```
cp $HOME/.alignedlayer/data/priv_validator_state.json $HOME/priv_validator_state.json
```

* Delete the data:

```
alignedlayerd tendermint unsafe-reset-all --home $HOME/.alignedlayer
```

#### Snap:

```
wget $(curl -s https://services.staketab.org/backend/aligned-testnet/ | jq -r .snap_link)
tar -xf $(curl -s https://services.staketab.org/backend/aligned-testnet/ | jq -r .snap_filename) -C $HOME/.alignedlayer/data/
```

#### Addrbook:

```
wget -O $HOME/.alignedlayer/config/addrbook.json $(curl -s https://services.staketab.org/backend/aligned-testnet/ | jq -r .addrbook_link)
```

#### Return your priv\_validator\_state.json <a href="#return-your-priv_validator_statejson" id="return-your-priv_validator_statejson"></a>

```
mv $HOME/priv_validator_state.json $HOME/.alignedlayer/data/priv_validator_state.json
```

#### Start the service and check logs: <a href="#start-the-service-and-check-logs" id="start-the-service-and-check-logs"></a>

```
sudo systemctl start alignedlayerd.service
sudo journalctl -u alignedlayerd.service -f --line 100
```
