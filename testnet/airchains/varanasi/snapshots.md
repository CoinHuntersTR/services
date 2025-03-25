# Snapshots

#### Stop the service and reset the data <a href="#stop-the-service-and-reset-the-data" id="stop-the-service-and-reset-the-data"></a>

```
sudo systemctl stop junctiond
cp $HOME/.junctiond/data/priv_validator_state.json $HOME/.junctiond/priv_validator_state.json.backup
rm -rf $HOME/.junctiond/data
```

#### Download latest snapshot <a href="#download-latest-snapshot" id="download-latest-snapshot"></a>

```
curl -L https://snapshots.coinhunterstr.com/testnet/airchains/snapshot_latest.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.junctiond
mv $HOME/.junctiond/priv_validator_state.json.backup $HOME/.junctiond/data/priv_validator_state.json
```

#### Restart the service and check the log <a href="#restart-the-service-and-check-the-log" id="restart-the-service-and-check-the-log"></a>

```
sudo systemctl start junctiond && sudo journalctl -u junctiond -f --no-hostname -o cat
```
