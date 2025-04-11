# Snapshot

#### Stop the service and reset the data <a href="#stop-the-service-and-reset-the-data" id="stop-the-service-and-reset-the-data"></a>

```
sudo systemctl stop babylond
cp $HOME/.babylond/data/priv_validator_state.json $HOME/.babylond/priv_validator_state.json.backup
rm -rf $HOME/.babylond/data
rm -rf $HOME/.babylond/wasm
rm -rf $HOME/.babylond/ibc_08-wasm
babylond tendermint unsafe-reset-all --home ~/.babylond/ --keep-addr-book
```

#### Download latest snapshot <a href="#download-latest-snapshot" id="download-latest-snapshot"></a>

```
curl -L https://snapshots.coinhunterstr.com/mainnet/babylon/snapshot_latest.tar.lz4 | lz4 -c -d - | tar -x -C $HOME/.babylond
mv $HOME/.babylond/priv_validator_state.json.backup $HOME/.babylond/data/priv_validator_state.json
```

#### Restart the service and check the log <a href="#restart-the-service-and-check-the-log" id="restart-the-service-and-check-the-log"></a>

```
sudo systemctl restart babylond && sudo journalctl -fu babylond -o cat
```
