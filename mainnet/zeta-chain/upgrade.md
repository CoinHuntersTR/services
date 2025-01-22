# Upgrade

v3.2.0

Upgrade height: [5135550](https://explorer.coinhunterstr.com/Dymension/block/5135550)  Please don\`t upgrade before the specified height;

### Manul Upgrade

```
cd $HOME
wget -O dymd https://github.com/dymensionxyz/dymension/releases/download/v3.2.0/dymd
chmod +x $HOME/dymd
sudo mv $HOME/dymd $(which dymd)
sudo systemctl restart dymd && sudo journalctl -u dymd -f
```

### Auto Upgrade

```
cd $HOME && \
wget -O dymd https://github.com/dymensionxyz/dymension/releases/download/v3.2.0/dymd && \
chmod +x $HOME/dymd && \
old_bin_path=$(which dymd) && \
home_path=$HOME && \
rpc_port=$(grep -m 1 -oP '^laddr = "\K[^"]+' "$HOME/.dymension/config/config.toml" | cut -d ':' -f 3) && \
[[ -z "$rpc_port" ]] && rpc_port=$(grep -oP 'node = "tcp://[^:]+:\K\d+' "$HOME/.dymension/config/client.toml") ; \
tmux new -s dym-upgrade "sudo bash -c 'curl -s https://raw.githubusercontent.com/CoinHuntersTR/Logo/refs/heads/main/autoupgrade/upgrade.sh | bash -s -- -u \"5135550\" -b dymd -n \"$HOME/dymd\" -o \"$old_bin_path\" -h \"$home_path\" -p \"https://explorer.coinhunterstr.com/Dymension/gov/24\" -r \"$rpc_port\"'"
```
