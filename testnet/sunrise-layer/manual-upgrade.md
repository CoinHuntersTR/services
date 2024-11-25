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

### Auto Upgrade

**Version v0.2.5**

> Sunucu içinde yeni bir screen açıyoruz. `screen -S sunrise` sonrasında aşağıdaki komutları tek seferde yapıştırıyoruz. İşlem başladığında geri sayıma geçecek. Sonrasında CTRL A D ile çıkıyoruz.&#x20;

```
cd $HOME && \
wget -O sunrised https://github.com/sunriselayer/sunrise/releases/download/v0.2.5-rc1/sunrised && \
chmod +x $HOME/sunrised && \
old_bin_path=$(which sunrised) && \
home_path=$HOME && \
rpc_port=$(grep -m 1 -oP '^laddr = "\K[^"]+' "$HOME/.lava/config/config.toml" | cut -d ':' -f 3) && \
[[ -z "$rpc_port" ]] && rpc_port=$(grep -oP 'node = "tcp://[^:]+:\K\d+' "$HOME/.sunrise/config/client.toml") ; \
tmux new -s sunrise-upgrade "sudo bash -c 'curl -s https://raw.githubusercontent.com/CoinHuntersTR/Logo/refs/heads/main/autoupgrade/upgrade.sh | bash -s -- -u \"813800\" -b lavad -n \"$HOME/sunrised\" -o \"$old_bin_path\" -h \"$home_path\" -p \"https://sunrise-api.chainad.org/cosmos/gov/v1/proposals/7\" -r \"$rpc_port\"'"
```
