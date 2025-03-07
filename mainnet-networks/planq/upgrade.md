# Upgrade



```
git clone https://github.com/planq-network/planq.git
cd planq/
git fetch --all
git checkout v2.1.0
make build
```



## Auto



```
cd $HOME && \
rm -rf planq && \
git clone https://github.com/planq-network/planq.git && \
cd planq && \
git checkout v2.1.0 && \
make build && \
old_bin_path=$(which planqd) && \
home_path=$HOME && \
rpc_port=$(grep -m 1 -oP '^laddr = "\K[^"]+' "$HOME/.planqd/config/config.toml" | cut -d ':' -f 3) && \
tmux new -s planq-upgrade "sudo bash -c 'curl -s https://raw.githubusercontent.com/appieasahbie/testnet-guides/main/utils/autoupgrade/upgrade.sh | bash -s -- -u \"13593520\" -b planqd -n \"$HOME/planq/build/planqd\" -o \"$old_bin_path\" -h \"$home_path\" -p \"undefined\" -r \"$rpc_port\"'"

```
