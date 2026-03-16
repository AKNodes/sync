# Upgrade

### Manually & Auto

```
cd $HOME
rm -rf wardenprotocol
git clone https://github.com/warden-protocol/wardenprotocol.git
cd wardenprotocol
git checkout v1.0.0
make install-wardend
sudo mv wardend $HOME/go/bin/
sudo systemctl restart wardend && sudo journalctl -u wardend -fo cat
```

```
cd $HOME && \
wget -O wardend https://github.com/warden-protocol/wardenprotocol/releases/download/v1.0.0/wardend-v1.0.0-linux-amd64 && \
chmod +x wardend && \
old_bin_path=/usr/local/bin/wardend && \
home_path=$HOME && \
rpc_port=$(grep -m1 -oP '^laddr = "\K[^"]+' $HOME/.warden/config/config.toml | cut -d':' -f3) && \
tmux new -s warden-upgrade "sudo bash -c 'curl -s https://raw.githubusercontent.com/appieasahbie/testnet-guides/main/utils/autoupgrade/upgrade.sh | bash -s -- -u \"5418000\" -b wardend -n \"$HOME/wardend\" -o \"$old_bin_path\" -h \"$home_path\" -p \"https://api.warden.aknodes.net/cosmos/gov/v1/proposals/5\" -r \"$rpc_port\"'"
```

