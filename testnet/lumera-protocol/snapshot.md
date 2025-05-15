# Snapshot

#### Stop the service and reset the data <a href="#stop-the-service-and-reset-the-data" id="stop-the-service-and-reset-the-data"></a>

```
sudo systemctl stop lumerad
cp $HOME/.lumera/data/priv_validator_state.json $HOME/.lumera/priv_validator_state.json.backup
rm -rf $HOME/.lumera/data
rm -rf $HOME/.lumera/wasm
lumerad tendermint unsafe-reset-all --home ~/.lumera/ --keep-addr-book
```

#### Download latest snapshot <a href="#download-latest-snapshot" id="download-latest-snapshot"></a>

```
curl -L https://snapshots.coinhunterstr.com/testnet/lumera/lumera/lumera_snapshot_latest.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.lumera
mv $HOME/.lumera/priv_validator_state.json.backup $HOME/.lumera/data/priv_validator_state.json
```

#### Restart the service and check the log <a href="#restart-the-service-and-check-the-log" id="restart-the-service-and-check-the-log"></a>

```
sudo systemctl restart lumerad && sudo journalctl -u lumerad -f --no-hostname -o cat
```
