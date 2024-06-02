# Upgrade

### Manual  <a href="#manual" id="manual"></a>

```bash
cd $HOME
wget https://github.com/crossfichain/crossfi-node/releases/download/v0.3.0-prebuild9/crossfi-node_0.3.0-prebuild9_linux_amd64.tar.gz
tar -xvf crossfi-node_0.3.0-prebuild9_linux_amd64.tar.gz
chmod +x $HOME/bin/crossfid
rm crossfi-node_0.3.0-prebuild9_linux_amd64.tar.gz
sudo mv $HOME/bin/crossfid $(which crossfid)
sudo systemctl restart crossfid && sudo journalctl -u crossfid -f
```

### Auto  <a href="#auto" id="auto"></a>

```bash
cd $HOME && \
wget https://github.com/crossfichain/crossfi-node/releases/download/v0.3.0-prebuild9/crossfi-node_0.3.0-prebuild9_linux_amd64.tar.gz && \
tar -xvf crossfi-node_0.3.0-prebuild9_linux_amd64.tar.gz && \
chmod +x $HOME/bin/crossfid && \
rm crossfi-node_0.3.0-prebuild9_linux_amd64.tar.gz && \
old_bin_path=$(which crossfid) && \
home_path=$HOME && \
rpc_port=$(grep -m 1 -oP '^laddr = "\K[^"]+' "$HOME/.mineplex-chain/config/config.toml" | cut -d ':' -f 3) && \
[[ -z "$rpc_port" ]] && rpc_port=$(grep -oP 'node = "tcp://[^:]+:\K\d+' "$HOME/.mineplex-chain/config/client.toml") ; \
tmux new -s crossfi-upgrade "sudo bash -c 'curl -s https://raw.githubusercontent.com/itrocket-team/testnet_guides/main/utils/autoupgrade/upgrade.sh | bash -s -- -u \"3520000\" -b crossfid -n \"$HOME/bin/crossfid\" -o \"$old_bin_path\" -h \"$home_path\" -p \"https://api.crossfi.aknodes.net/cosmos/gov/v1/proposals/5\" -r \"$rpc_port\"'"
```
