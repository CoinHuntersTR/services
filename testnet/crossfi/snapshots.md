# Snapshots

#### Stop the service and reset the data <a href="#stop-the-service-and-reset-the-data" id="stop-the-service-and-reset-the-data"></a>

```
sudo systemctl stop crossfid
cp $HOME/.mineplex-chain/data/priv_validator_state.json $HOME/.mineplex-chain/priv_validator_state.json.backup
rm -rf $HOME/.mineplex-chain/data
```

#### Download latest snapshot <a href="#download-latest-snapshot" id="download-latest-snapshot"></a>

```
curl -L https://snapshots.coinhunterstr.com/snap_crossfi.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.mineplex-chain
mv $HOME/.mineplex-chain/priv_validator_state.json.backup $HOME/.mineplex-chain/data/priv_validator_state.json
```

#### Restart the service and check the log <a href="#restart-the-service-and-check-the-log" id="restart-the-service-and-check-the-log"></a>

```
sudo systemctl start crossfid && sudo journalctl -u crossfid -f --no-hostname -o cat
```
