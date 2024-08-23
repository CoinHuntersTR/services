# Snapshots

#### Her 24 saatte bir Snapshot Alır ve yayınlar. <a href="#stop-the-service-and-reset-the-data" id="stop-the-service-and-reset-the-data"></a>

#### Stop the service and reset the data <a href="#stop-the-service-and-reset-the-data" id="stop-the-service-and-reset-the-data"></a>

```
sudo systemctl stop allora.service
cp $HOME/.allorad/data/priv_validator_state.json $HOME/.allorad/priv_validator_state.json.backup
rm -rf $HOME/.allorad/data $HOME/.allorad/wasm
```

#### Download latest snapshot <a href="#download-latest-snapshot" id="download-latest-snapshot"></a>

```
curl https://snapshots.coinhunterstr.com/allora/snapshot_latest.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.allorad
mv $HOME/.allorad/priv_validator_state.json.backup $HOME/.allorad/data/priv_validator_state.json
```

#### Restart the service and check the log <a href="#restart-the-service-and-check-the-log" id="restart-the-service-and-check-the-log"></a>

```
sudo systemctl restart allora.service && sudo journalctl -u allora.service -f
```
