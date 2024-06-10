# Snapshots

#### Stop the service and reset the data <a href="#stop-the-service-and-reset-the-data" id="stop-the-service-and-reset-the-data"></a>

```bash
sudo systemctl stop nillion.service
cp $HOME/.nillionapp/data/priv_validator_state.json $HOME/.nillionapp/priv_validator_state.json.backup
rm -rf $HOME/.nillionapp/data
```

#### Download latest snapshot <a href="#download-latest-snapshot" id="download-latest-snapshot"></a>

```bash
curl -L https://snapshots.coinhunterstr.com/nillion/snapshot_latest.tar.lz4 | tar -Ilz4 -xf - -C $HOME/.nillionapp
mv $HOME/.nillionapp/priv_validator_state.json.backup $HOME/.nillionapp/data/priv_validator_state.json
```

#### Restart the service and check the log <a href="#restart-the-service-and-check-the-log" id="restart-the-service-and-check-the-log"></a>

```
sudo systemctl restart nillion.service && sudo journalctl -u nillion.service -f --no-hostname -o cat
```
