# Upgrade

### story-geth v.0.9.4

```
# Clone project repository
cd $HOME
rm -rf story-geth
git clone https://github.com/piplabs/story-geth.git
cd story-geth
git checkout v0.9.4

# Build binaries
make geth
sudo mv build/bin/geth $(which geth)
```

### story v.11.0

```
# Clone project repository
cd $HOME
rm -rf story
git clone https://github.com/piplabs/story.git
cd story
git checkout v0.11.0

# Build binaries
go build -o story ./client

# Prepare Cosmovisor upgrade
DAEMON_NAME=story
DAEMON_HOME=$HOME/.story/story
cosmovisor add-upgrade v0.11.0 story --upgrade-height 1325860 --force
```
