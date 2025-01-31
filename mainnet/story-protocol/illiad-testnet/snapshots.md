---
description: updated every 24h.
---

# Snapshots

```
# install dependencies, and disable statesync to avoid sync issues
sudo apt install curl tmux jq lz4 unzip -y
sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1false|" $HOME/.story/story/config/config.toml

# stop node and backup priv_validator_state.json
sudo systemctl stop story story-geth
cp $HOME/.story/story/data/priv_validator_state.json $HOME/.story/story/priv_validator_state.json.backup

# remove old data and unpack Story snapshot
rm -rf $HOME/.story/story/data
curl https://snapshots.coinhunterstr.com/mainnet/story-dev/story/story_snapshot_latest.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.story/story

# restore priv_validator_state.json
mv $HOME/.story/story/priv_validator_state.json.backup $HOME/.story/story/data/priv_validator_state.json

# delete geth data and unpack Geth snapshot
rm -rf $HOME/.story/geth/story/geth/chaindata
curl https://snapshots.coinhunterstr.com/mainnet/story-dev/story-geth/story_geth_snapshot_latest.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.story/geth/story/geth

# restart node and check logs
sudo systemctl restart story story-geth
sudo journalctl -u story-geth -u story -f
```
