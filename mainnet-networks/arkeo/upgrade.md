# Upgrade

```
cd $HOME
rm -rf arkeo
git clone https://github.com/arkeonetwork/arkeo.git
cd arkeo
git checkout v1.0.9
make install
```



## Auto

```
cd $HOME && \
wget -O arkeod.zip https://github.com/arkeonetwork/arkeo/releases/download/v1.0.11/arkeod_1.0.11_linux_amd64.zip && \
unzip -o arkeod.zip && \
chmod +x arkeod && \
old_bin_path=$(which arkeod) && \
home_path=$HOME && \
rpc_port=$(grep -m 1 -oP '^laddr = "\K[^"]+' "$HOME/.arkeo/config/config.toml" | cut -d ':' -f 3) && \
tmux new -s arkeo-upgrade "sudo bash -c 'curl -s https://raw.githubusercontent.com/appieasahbie/testnet-guides/main/utils/autoupgrade/upgrade.sh | bash -s -- -u \"252000\" -b arkeod -n \"$HOME/arkeod\" -o \"$old_bin_path\" -h \"$home_path\" -p \"https://api.arkeo-mainnet.aknodes.net/cosmos/gov/v1/proposals/8\" -r \"$rpc_port\"'"
```
