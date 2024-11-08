# Snapshots

#### Her 24 saatte bir Snapshot Alır ve yayınlar. <a href="#stop-the-service-and-reset-the-data" id="stop-the-service-and-reset-the-data"></a>

#### Stop the service and reset the data <a href="#stop-the-service-and-reset-the-data" id="stop-the-service-and-reset-the-data"></a>

```
sudo systemctl stop sided
cp $HOME/.side/data/priv_validator_state.json $HOME/.side/priv_validator_state.json.backup
rm -rf $HOME/.side/data $HOME/.side/wasm
```

#### Download latest snapshot <a href="#download-latest-snapshot" id="download-latest-snapshot"></a>

```
curl https://snapshots.coinhunterstr.com/testnet/side/snapshot_latest.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.sunrise
mv $HOME/.side/priv_validator_state.json.backup $HOME/.side/data/priv_validator_state.json
```

#### Restart the service and check the log <a href="#restart-the-service-and-check-the-log" id="restart-the-service-and-check-the-log"></a>

```
sudo systemctl restart sided && sudo journalctl -u sided-f
```
