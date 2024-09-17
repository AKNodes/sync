# Upgrade

### Upgarde Manual



```
cd $HOME
wget -O lavad https://github.com/lavanet/lava/releases/download/v3.1.0/lavad-v3.1.0-linux-amd64
chmod +x $HOME/lavad
sudo mv $HOME/lavad $(which lavad)
sudo systemctl restart lavad && sudo journalctl -u lavad -f
```



### Auto



```
cd $HOME && \
wget -O lavad https://github.com/lavanet/lava/releases/download/v3.1.0/lavad-v3.1.0-linux-amd64 && \
chmod +x $HOME/lavad && \
old_bin_path=$(which lavad) && \
home_path=$HOME && \
rpc_port=$(grep -m 1 -oP '^laddr = "\K[^"]+' "$HOME/.lava/config/config.toml" | cut -d ':' -f 3) && \
tmux new -s lava-upgrade "sudo bash -c 'curl -s https://raw.githubusercontent.com/appieasahbie/testnet-guides/main/utils/autoupgrade/upgrade.sh | bash -s -- -u \"888500\" -b lavad -n \"$HOME/lavad\" -o \"$old_bin_path\" -h \"$home_path\" -p \"https://lava-api.w3coins.io/cosmos/gov/v1/proposals/6\" -r \"$rpc_port\"'"
```
