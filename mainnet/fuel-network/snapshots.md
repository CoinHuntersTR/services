# Snapshots

#### Stop the service and reset the data <a href="#stop-the-service-and-reset-the-data" id="stop-the-service-and-reset-the-data"></a>

```markdown
sudo systemctl stop fuelsequencerd
cp $HOME/.fuelsequencer/data/priv_validator_state.json $HOME/.fuelsequencer/priv_validator_state.json.backup
rm -rf $HOME/.fuelsequencer/data
```

#### Download latest snapshot <a href="#download-latest-snapshot" id="download-latest-snapshot"></a>

```markdown
curl https://snapshots.coinhunterstr.com/mainnet/fuel/snapshot_latest.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.fuelsequencer
mv $HOME/.fuelsequencer/priv_validator_state.json.backup $HOME/.fuelsequencer/data/priv_validator_state.json
```

#### Restart the service and check the log <a href="#restart-the-service-and-check-the-log" id="restart-the-service-and-check-the-log"></a>

```markdown
sudo systemctl restart fuelsequencerd && sudo journalctl -fu fuelsequencerd -o cat
```
