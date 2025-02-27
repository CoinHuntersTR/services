# Upgrade

### story-geth upgraded to v1.0.2

```
sudo systemctl stop story story-geth
cd $HOME
wget https://github.com/piplabs/story-geth/releases/download/v1.0.2/geth-linux-arm64
sudo mv ./geth-linux-arm64 story-geth
sudo chmod +x story-geth
sudo mv ./story-geth $HOME/go/bin/story-geth

sudo systemctl restart story-geth && sleep 5 && sudo systemctl restart story
journalctl -u story -u story-geth -f
```
