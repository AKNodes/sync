# Upgrade

### Upgrade height: 11235000

### Manual upgrade <a href="#manual" id="manual"></a>

```bash
cd $HOME
rm -rf haqq
git clone https://github.com/haqq-network/haqq.git
cd haqq
git checkout v1.7.5
make build
sudo mv $HOME/haqq/build/haqqd $(which haqqd)
sudo systemctl restart haqqd && sudo journalctl -u haqqd -f
```

### Auto upgrade <a href="#auto" id="auto"></a>

Don't kill the session with CTRL+C before update is completed, if you want to disconnect the session use CTRL+B D

```bash
cd $HOME && \
rm -rf haqq && \
git clone https://github.com/haqq-network/haqq.git && \
cd haqq && \
git checkout v1.7.5 && \
make build && \
old_bin_path=$(which haqqd) && \
home_path=$HOME && \
rpc_port=$(grep -m 1 -oP '^laddr = "\K[^"]+' "$HOME/.haqqd/config/config.toml" | cut -d ':' -f 3) && \
tmux new -s haqq-upgrade "sudo bash -c 'curl -s https://raw.githubusercontent.com/appieasahbie/testnet-guides/main/utils/autoupgrade/upgrade.sh | bash -s -- -u \"11235000\" -b haqqd -n \"$HOME/haqq/build/haqqd\" -o \"$old_bin_path\" -h \"$home_path\" -p \"https://api.haqq.aknodes.net/cosmos/gov/v1/proposals/32\" -r \"$rpc_port\"'"
```
