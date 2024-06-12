# Snapshots

#### Stop the service and reset the data <a href="#stop-the-service-and-reset-the-data" id="stop-the-service-and-reset-the-data"></a>

```markdown
sudo systemctl stop dymension.service
cp $HOME/.dymension/data/priv_validator_state.json $HOME/.dymension/priv_validator_state.json.backup
rm -rf $HOME/.dymension/data
```

#### Download latest snapshot <a href="#download-latest-snapshot" id="download-latest-snapshot"></a>

```markdown
curl -o - -L https://snapshots.coinhunterstr.com/dymension/snapshot_latest.tar.lz4 | lz4 -c -d - | tar -x -C $HOME/.artelad
mv $HOME/.artelad/priv_validator_state.json.backup $HOME/.artelad/data/priv_validator_state.json
```

#### Restart the service and check the log <a href="#restart-the-service-and-check-the-log" id="restart-the-service-and-check-the-log"></a>

```markdown
sudo systemctl start dymension.service && sudo journalctl -u dymension.service -f --no-hostname -o cat
```
