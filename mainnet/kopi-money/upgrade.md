# Upgrade

v7-rc6

### Manul Upgrade

```
cd $HOME
wget -O kopid https://github.com/kopi-money/kopi/releases/download/v7/kopid-v7-linux-amd64
chmod +x $HOME/kopid
sudo mv $HOME/kopid $(which kopid)
```

### libwasmvm.x86_64.so

```
cd $HOME
wget https://github.com/kopi-money/kopi/releases/download/v7/libwasmvm.x86_64.so
sudo mv libwasmvm.x86_64.so /usr/lib/
sudo ldconfig
```

```
sudo systemctl restart kopid.service
sudo journalctl -u kopid.service -f
```
