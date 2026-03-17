# Upgrade



```
cd $HOME && \
rm -rf axoned && \
git clone https://github.com/axone-protocol/axoned && \
cd axoned && \
git checkout v14.0.0 && \
make install && \
old_bin_path=/usr/local/bin/axoned && \
home_path=$HOME && \
rpc_port=$(grep -m1 -oP '^laddr = "\K[^"]+' $HOME/.axoned/config/config.toml | cut -d':' -f3) && \
tmux new -s axone-upgrade "sudo bash -c 'curl -s https://raw.githubusercontent.com/appieasahbie/testnet-guides/main/utils/autoupgrade/upgrade.sh | bash -s -- -u \"2802853\" -b axoned -n \"$HOME/go/bin/axoned\" -o \"$old_bin_path\" -h \"$home_path\" -p \"https://explorer.aknodes.com/AXONE-TESTNET/gov/1\" -r \"$rpc_port\"'"
```
