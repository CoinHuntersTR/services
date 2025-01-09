# Snapshots

#### Her 12 saatte bir Snapshot Alır ve yayınlar. <a href="#stop-the-service-and-reset-the-data" id="stop-the-service-and-reset-the-data"></a>

#### Stop the service and reset the data <a href="#stop-the-service-and-reset-the-data" id="stop-the-service-and-reset-the-data"></a>

```
sudo systemctl stop gonatived
cp $HOME/.gonative/data/priv_validator_state.json $HOME/.gonative/priv_validator_state.json.backup
rm -rf $HOME/.gonative/data
```

#### Download latest snapshot <a href="#download-latest-snapshot" id="download-latest-snapshot"></a>

```
curl https://snapshots.coinhunterstr.com/testnet/native/snapshot_latest.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.gonative
mv $HOME/.gonative/priv_validator_state.json.backup $HOME/.gonative/data/priv_validator_state.json
```

#### Restart the service and check the log <a href="#restart-the-service-and-check-the-log" id="restart-the-service-and-check-the-log"></a>

```
sudo systemctl restart gonatived && sudo journalctl -fu gonatived -o cat
```
