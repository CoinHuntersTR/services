# Upgrade

v7-rc6

### Manul Upgrade

```
cd $HOME
wget -O kopid https://github.com/kopi-money/kopi/releases/download/v7/kopid-v7-linux-amd64
chmod +x $HOME/kopid
sudo mv $HOME/kopid $(which kopid)
```

### libwasmvm.x86\_64.so

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

### v8 Upgrade

Upgrade height: [3930000](https://explorer.coinhunterstr.com/Kopi-Money/block/3930000) Please don\`t upgrade before the specified height;

### Manul Upgrade

```
cd $HOME
wget -O kopid https://github.com/kopi-money/kopi/releases/download/v8/kopid-v8-linux-amd64
chmod +x $HOME/kopid
sudo mv $HOME/kopid $(which kopid)
sudo systemctl restart kopid.service && sudo journalctl -u kopid.service -f
```

### Auto Upgrade

İlk olarak screen açalım `screen -S upgrade` sonrasında alttaki kod bloğunu çalıştıralım.

```
cd $HOME && \
wget -O kopid https://github.com/kopi-money/kopi/releases/download/v8/kopid-v8-linux-amd64 && \
chmod +x $HOME/kopid && \
old_bin_path=$(which kopid) && \
home_path=$HOME && \
rpc_port=$(grep -m 1 -oP '^laddr = "\K[^"]+' "$HOME/.kopid/config/config.toml" | cut -d ':' -f 3) && \
[[ -z "$rpc_port" ]] && rpc_port=$(grep -oP 'node = "tcp://[^:]+:\K\d+' "$HOME/.kopid/config/client.toml") ; \
tmux new -s kopi-upgrade "sudo bash -c 'curl -s https://raw.githubusercontent.com/CoinHuntersTR/Logo/refs/heads/main/autoupgrade/upgrade.sh | bash -s -- -u \"3930000\" -b kopid.service -n \"$HOME/kopid.service\" -o \"$old_bin_path\" -h \"$home_path\" -p \"https://explorer.coinhunterstr.com/Kopi-Money/gov/30\" -r \"$rpc_port\"'"
```
