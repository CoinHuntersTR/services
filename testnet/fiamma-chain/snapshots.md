# Snapshots

#### Her 6 saatte bir Snapshot Alır ve yayınlar. <a href="#stop-the-service-and-reset-the-data" id="stop-the-service-and-reset-the-data"></a>

#### Stop the service and reset the data <a href="#stop-the-service-and-reset-the-data" id="stop-the-service-and-reset-the-data"></a>

```
sudo systemctl stop fiammad
cp $HOME/.fiamma/data/priv_validator_state.json $HOME/.fiamma/priv_validator_state.json.backup
rm -rf $HOME/.fiamma/data $HOME/.fiamma/wasm
```

#### Download latest snapshot <a href="#download-latest-snapshot" id="download-latest-snapshot"></a>

```
curl https://snapshots.coinhunterstr.com/testnet/fiamma/snapshot_latest.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.fiamma
mv $HOME/.fiamma/priv_validator_state.json.backup $HOME/.fiamma/data/priv_validator_state.json
```

#### Restart the service and check the log <a href="#restart-the-service-and-check-the-log" id="restart-the-service-and-check-the-log"></a>

```
sudo systemctl restart fiammad && sudo journalctl -u fiammad -f --no-hostname -o cat
```
