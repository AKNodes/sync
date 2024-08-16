# Upgrade



```
cd $HOME
wget https://github.com/MANTRA-Finance/public/releases/download/v2.0.0/mantrachaind-2.0.0-linux-amd64.tar.gz
tar -xvf mantrachaind-2.0.0-linux-amd64.tar.gz
rm $HOME/mantrachaind-2.0.0-linux-amd64.tar.gz
chmod +x mantrachaind
sudo mv $HOME/mantrachaind $(which mantrachaind)
sudo systemctl restart mantrachaind && sudo journalctl -u mantrachaind -f
```



### Auto



```
cd $HOME && \
wget -O mantrachaind https://github.com/MANTRA-Finance/public/releases/download/v3.0.0/mantrachaind-3.0.0-linux-amd64.tar.gz && \
chmod +x $HOME/mantrachaind && \
old_bin_path=$(which mantrachaind) && \
home_path=$HOME && \
rpc_port=$(grep -m 1 -oP '^laddr = "\K[^"]+' "$HOME/.mantrachain/config/config.toml" | cut -d ':' -f 3) && \
tmux new -s mantrachain-upgrade "sudo bash -c 'curl -s https://raw.githubusercontent.com/appieasahbie/testnet-guides/main/utils/autoupgrade/upgrade.sh | bash -s -- -u \"1687000\" -b lavad -n \"$HOME/lavad\" -o \"$old_bin_path\" -h \"$home_path\" -p \"https://mantra-testnet-api.itrocket.net//cosmos/gov/v1/proposals/3\" -r \"$rpc_port\"'"
```
