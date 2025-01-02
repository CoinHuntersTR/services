# Snapshot

#### Stop the service and reset the data <a href="#stop-the-service-and-reset-the-data" id="stop-the-service-and-reset-the-data"></a>

```
sudo systemctl stop kopid.service
cp $HOME/.kopid/data/priv_validator_state.json $HOME/.kopid/priv_validator_state.json.backup
rm -rf $HOME/.kopid/data
```

#### Download latest snapshot <a href="#download-latest-snapshot" id="download-latest-snapshot"></a>

```
curl -L https://snapshots.coinhunterstr.com/mainnet/mantra/snapshot_latest.tar.lz4 | lz4 -c -d - | tar -x -C $HOME/.mantrachain
mv $HOME/.mantrachain/priv_validator_state.json.backup $HOME/.mantrachain/data/priv_validator_state.json
```

#### Restart the service and check the log <a href="#restart-the-service-and-check-the-log" id="restart-the-service-and-check-the-log"></a>

```
sudo systemctl restart mantrachaind && sudo journalctl -u mantrachaind -f --no-hostname -o cat
```
