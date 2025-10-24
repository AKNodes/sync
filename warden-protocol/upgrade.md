# Upgrade

### Manually

```
cd $HOME
rm -rf wardenprotocol
git clone https://github.com/warden-protocol/wardenprotocol.git
cd wardenprotocol
git checkout v0.7.2
make install-wardend
sudo mv wardend $HOME/go/bin/
sudo systemctl restart wardend && sudo journalctl -u wardend -fo cat
```

```
cd $HOME && \
wget -O wardend https://github.com/warden-protocol/wardenprotocol/releases/download/v0.7.2/wardend-v0.7.2-linux-amd64 && \
chmod +x $HOME/wardend && \
old_bin_path=$(which wardend) && \
home_path=$HOME && \
rpc_port=$(grep -m 1 -oP '^laddr = "\K[^"]+' "$HOME/.warden/config/config.toml" | cut -d ':' -f 3) && \
tmux new -s warden-upgrade "sudo bash -c 'curl -s https://raw.githubusercontent.com/appieasahbie/testnet-guides/main/utils/autoupgrade/upgrade.sh | bash -s -- -u \"1310000\" -b wardend -n \"$HOME/lavad\" -o \"$old_bin_path\" -h \"$home_path\" -p \"https://api.warden.aknodes.net//cosmos/gov/v1/proposals/4\" -r \"$rpc_port\"'"
```

