# Snapshots

#### Stop the service and reset the data <a href="#stop-the-service-and-reset-the-data" id="stop-the-service-and-reset-the-data"></a>

```markdown
sudo systemctl stop sided
cp $HOME/.side/data/priv_validator_state.json $HOME/.side/priv_validator_state.json.backup
rm -rf $HOME/.side/data
```

#### Download latest snapshot <a href="#download-latest-snapshot" id="download-latest-snapshot"></a>

```markdown
curl -L https://snapshots.coinhunterstr.com/mainnet/side/snapshot_latest.tar.lz4 | tar -Ilz4 -xf - -C $HOME/.side
mv $HOME/.side/priv_validator_state.json.backup $HOME/.side/data/priv_validator_state.json
```

#### Restart the service and check the log <a href="#restart-the-service-and-check-the-log" id="restart-the-service-and-check-the-log"></a>

```markdown
sudo systemctl restart sided && sudo journalctl -u sided -fo cat
```
