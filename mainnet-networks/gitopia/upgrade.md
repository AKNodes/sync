# Upgrade

## Manually

```
cd $HOME
rm -rf gitopia
git clone https://github.com/gitopia/gitopia.git
cd gitopia
git checkout v5.0.1
make build
sudo systemctl restart gitopiad
sudo journalctl -u gitopiad -f --no-hostname -o cat
```



### Auto

```
cd $HOME && \
wget -O gitopiad.tar.gz https://github.com/gitopia/gitopia/releases/download/v5.0.1/gitopiad_5.0.1_linux_amd64.tar.gz && \
tar -xzf gitopiad.tar.gz && \
chmod +x $HOME/gitopiad && \
old_bin_path=$(which gitopiad) && \
home_path=$HOME && \
rpc_port=$(grep -m 1 -oP '^laddr = "\K[^"]+' "$HOME/.gitopia/config/config.toml" | cut -d ':' -f 3) && \
tmux new -s gitopia-upgrade "sudo bash -c 'curl -s https://raw.githubusercontent.com/appieasahbie/testnet-guides/main/utils/autoupgrade/upgrade.sh | bash -s -- -u \"24330422\" -b \"$HOME/gitopiad\" -n \"$HOME/gitopiad\" -o \"$old_bin_path\" -h \"$home_path\" -p \"https://api.gitopia.aknodes.net//cosmos/gov/v1/proposals/31\" -r \"$rpc_port\"'"

```
