# Upgrade

## STORY-GETH UPGRADE

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

## STORY UPGRADE

### Story v1.3.1

```
sudo systemctl stop story
wget -O $(which story) https://github.com/piplabs/story/releases/download/v1.3.1/story-linux-arm64
chmod +x $(which story)
sudo systemctl restart geth && sleep 5 && sudo systemctl restart story
journalctl -u story -u geth -f
```

### Story v1.2.1

```
sudo systemctl stop story
wget -O $(which story) https://github.com/piplabs/story/releases/download/v1.2.1/story-linux-arm64
chmod +x $(which story)
sudo systemctl restart story-geth && sleep 5 && sudo systemctl restart story
journalctl -u story -u story-geth -f
```

### Story v1.2.0

```
# Servisi durdur
sudo systemctl stop story

# Yeni binary'yi indir
wget https://github.com/piplabs/story/releases/download/v1.2.0/story-linux-arm64
sudo mv story-linux-arm64 story
sudo chmod +x story
sudo mv ./story $HOME/go/bin/story

# Servisi tekrar başlat
sudo systemctl restart story-geth && sleep 5 && sudo systemctl restart story
journalctl -u story -u story-geth -f
```

### Story v1.1.3

```
sudo systemctl stop story

wget http://snapshots.coinhunterstr.com/site/story-linux-arm64-1.1.3-38313e8 -O story.tar

mkdir -p story_extract
tar -xf story.tar -C story_extract

chmod +x /root/story_extract/story-linux-arm64-1.1.3-38313e8/story
mv /root/story_extract/story-linux-arm64-1.1.3-38313e8/story $HOME/go/bin/story

sudo systemctl restart story

story version


rm -rf story.tar story_extract

sudo systemctl restart story-geth && sleep 5 && sudo systemctl restart story
journalctl -u story -u story-geth -f
```

### Story v1.1.1

```
# Servisi durdur
sudo systemctl stop story

# Yeni binary'yi indir
wget https://github.com/piplabs/story/releases/download/v1.1.1/story-linux-arm64
sudo mv story-linux-arm64 story
sudo chmod +x story
sudo mv ./story $HOME/go/bin/story

# Servisi tekrar başlat
sudo systemctl start story
```
