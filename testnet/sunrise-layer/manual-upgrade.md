# Manual upgrade

Version v0.2.0

```
cd $HOME
wget -O sunrised wget https://github.com/sunriselayer/sunrise/releases/download/v0.2.0/sunrised
chmod +x $HOME/sunrised
mv $HOME/sunrised $HOME/go/bin/sunrised
sudo systemctl restart sunrised && sudo journalctl -u sunrised -f
```
