# Upgrade

### Manual

```
cd $HOME
rm -rf $HOME/terp-core
git clone https://github.com/terpnetwork/terp-core.git
cd $HOME/terp-core
git checkout v4.2.0
make build
sudo mv $HOME/terp-core/build/terpd $(which terpd)
sudo systemctl restart terpd && sudo journalctl -u terpd -f
```

### **Auto**



```
cd $HOME && \
rm -rf $HOME/terp-core && \
git clone https://github.com/terpnetwork/terp-core.git && \
cd terp-core && \
git checkout v4.2.0 && \
make build && \
old_bin_path=$(which terpd) && \
home_path=$HOME && \
rpc_port=$(grep -m 1 -oP '^laddr = "\K[^"]+' "$HOME/.terp/config/config.toml" | cut -d ':' -f 3) && \
tmux new -s terp-upgrade "sudo bash -c 'curl -s https://raw.githubusercontent.com/appieasahbie/testnet-guides/main/utils/autoupgrade/upgrade.sh | bash -s -- -u \"5486131\" -b terpd -n \"$HOME/terp-core/build/terpd\" -o \"$old_bin_path\" -h \"$home_path\" -p \"" -r \"$rpc_port\"'"
```
