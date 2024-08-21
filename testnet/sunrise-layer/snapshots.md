# Snapshots

#### Her 24 saatte bir Snapshot Alır ve yayınlar. <a href="#stop-the-service-and-reset-the-data" id="stop-the-service-and-reset-the-data"></a>

#### Stop the service and reset the data <a href="#stop-the-service-and-reset-the-data" id="stop-the-service-and-reset-the-data"></a>

```
sudo systemctl stop sunrised
cp $HOME/.sunrise/data/priv_validator_state.json $HOME/.sunrise/priv_validator_state.json.backup
rm -rf $HOME/.sunrise/data $HOME/.sunrise/wasm
```

#### Download latest snapshot <a href="#download-latest-snapshot" id="download-latest-snapshot"></a>

```
curl https://snapshots.coinhunterstr.com/sunrise/snapshot_latest.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.sunrise
mv $HOME/.sunrise/priv_validator_state.json.backup $HOME/.sunrise/data/priv_validator_state.json
```

#### Restart the service and check the log <a href="#restart-the-service-and-check-the-log" id="restart-the-service-and-check-the-log"></a>

```
sudo systemctl restart sunrised && sudo journalctl -u sunrised -f
```
