# Snapshots

#### Her 24 saatte bir Snapshot Alır ve yayınlar. <a href="#stop-the-service-and-reset-the-data" id="stop-the-service-and-reset-the-data"></a>

#### Stop the service and reset the data <a href="#stop-the-service-and-reset-the-data" id="stop-the-service-and-reset-the-data"></a>

```
sudo systemctl stop emped
cp $HOME/.empe-chain/data/priv_validator_state.json $HOME/.empe-chain/priv_validator_state.json.backup
rm -rf $HOME/.empe-chain/data
```

#### Download latest snapshot <a href="#download-latest-snapshot" id="download-latest-snapshot"></a>

```
curl https://snapshots.coinhunterstr.com/empeiria/snapshot_latest.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.empe-chain
mv $HOME/.empe-chain/priv_validator_state.json.backup $HOME/.empe-chain/data/priv_validator_state.json
```

#### Restart the service and check the log <a href="#restart-the-service-and-check-the-log" id="restart-the-service-and-check-the-log"></a>

```
sudo systemctl restart emped && sudo journalctl -u emped -f
```
