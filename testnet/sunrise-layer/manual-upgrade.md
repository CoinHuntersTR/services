# Manual upgrade

Version v0.2.0

```
cd $HOME
wget -O sunrised wget https://github.com/sunriselayer/sunrise/releases/download/v0.2.0/sunrised
chmod +x $HOME/sunrised
mv $HOME/sunrised $HOME/go/bin/sunrised
sudo systemctl restart sunrised && sudo journalctl -u sunrised -f
```

Version v0.2.1

```
cd $HOME
wget -O sunrised wget https://github.com/sunriselayer/sunrise/releases/download/v0.2.1/sunrised
chmod +x $HOME/sunrised
mv $HOME/sunrised $HOME/go/bin/sunrised
sudo systemctl restart sunrised && sudo journalctl -u sunrised -f
```

Version v0.2.3 (update 554150 blok. - 23.10.2024)

```
cd $HOME
wget -O sunrised https://github.com/sunriselayer/sunrise/releases/download/v0.2.3/sunrised
chmod +x sunrised
sudo mv $HOME/sunrised $(which sunrised)
sudo systemctl restart sunrised && sudo journalctl -u sunrised -f
```
