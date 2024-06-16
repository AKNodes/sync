# Upgrade

### Upgrade to v2.1.3 at height: 1407000

### Manual upgrade <a href="#manual" id="manual"></a>

```bash
cd $HOME
wget -O lavad https://github.com/lavanet/lava/releases/download/v2.1.3/lavad-v2.1.3-linux-amd64
chmod +x $HOME/lavad
sudo mv $HOME/lavad $(which lavad)
sudo systemctl restart lavad && sudo journalctl -u lavad -f
```

### Auto upgrade <a href="#auto" id="auto"></a>



```bash
cd $HOME && \
wget -O lavad https://github.com/lavanet/lava/releases/download/v2.1.3/lavad-v2.1.3-linux-amd64 && \
chmod +x $HOME/lavad && \
old_bin_path=$(which lavad) && \
home_path=$HOME && \
rpc_port=$(grep -m 1 -oP '^laddr = "\K[^"]+' "$HOME/.lava/config/config.toml" | cut -d ':' -f 3) && \
tmux new -s lava-upgrade "sudo bash -c 'curl -s https://raw.githubusercontent.com/appieasahbie/testnet-guides/main/utils/autoupgrade/upgrade.sh | bash -s -- -u \"1407000\" -b lavad -n \"$HOME/lavad\" -o \"$old_bin_path\" -h \"$home_path\" -p \"https://api.lava.aknodes.net//cosmos/gov/v1/proposals/207\" -r \"$rpc_port\"'"
```

## Upgrade Lavap to v2.0.1



```
wget -O lavap https://github.com/lavanet/lava/releases/download/v2.0.1/lavap-v2.0.1-linux-amd64
chmod +x lavap
mv lavap $(which lavap)
sudo systemctl restart lavap && sudo journalctl -u lavap -f -o cat
```
