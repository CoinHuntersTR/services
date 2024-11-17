# Snapshots

#### Her 6 saatte bir Snapshot Alır ve yayınlar. <a href="#stop-the-service-and-reset-the-data" id="stop-the-service-and-reset-the-data"></a>

#### Stop the service and reset the data <a href="#stop-the-service-and-reset-the-data" id="stop-the-service-and-reset-the-data"></a>

```
sudo systemctl stop axoned
cp $HOME/.axoned/data/priv_validator_state.json $HOME/.axoned/priv_validator_state.json.backup
rm -rf $HOME/.axoned/data
```

#### Download latest snapshot <a href="#download-latest-snapshot" id="download-latest-snapshot"></a>

```
curl https://snapshots.coinhunterstr.com/testnet/axone/snapshot_latest.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.axoned
mv $HOME/.axoned/priv_validator_state.json.backup $HOME/.axoned/data/priv_validator_state.json
```

#### Restart the service and check the log <a href="#restart-the-service-and-check-the-log" id="restart-the-service-and-check-the-log"></a>

```
sudo systemctl restart axoned && sudo journalctl -u axoned -f --no-hostname -o cat
```
