# Upgrade

v25

Upgrade height: [6812000](https://explorer.coinhunterstr.com/Zetachain/block/6812000)  Please don\`t upgrade before the specified height;

### Manul Upgrade

```
cd $HOME
wget -O $HOME/zetacored https://github.com/zeta-chain/node/releases/download/v25.0.0/zetacored-linux-amd64
chmod +x $HOME/zetacored 
sudo mv $HOME/zetacored $(which zetacored)
sudo systemctl restart zetacored && sudo journalctl -u zetacored -f
```

### Auto Upgrade

```
cd $HOME && \
wget -O $HOME/zetacored https://github.com/zeta-chain/node/releases/download/v25.0.0/zetacored-linux-amd64 && \
chmod +x $HOME/zetacored  && \
old_bin_path=$(which zetacored) && \
home_path=$HOME && \
rpc_port=$(grep -m 1 -oP '^laddr = "\K[^"]+' "$HOME/.zetacored/config/config.toml" | cut -d ':' -f 3) && \
[[ -z "$rpc_port" ]] && rpc_port=$(grep -oP 'node = "tcp://[^:]+:\K\d+' "$HOME/.zetacored/config/client.toml") ; \
tmux new -s zetachain-upgrade "sudo bash -c 'curl -s https://raw.githubusercontent.com/CoinHuntersTR/Logo/refs/heads/main/autoupgrade/upgrade.sh | bash -s -- -u \"6812000\" -b zetacored -n \"$HOME/zetacored\" -o \"$old_bin_path\" -h \"$home_path\" -p \"https://explorer.coinhunterstr.com/Zetachain/gov/47\" -r \"$rpc_port\"'"
```


