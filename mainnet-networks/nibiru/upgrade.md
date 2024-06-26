---
description: Height 8375044
---

# Upgrade

**Manual**

```
cd $HOME
rm -rf nibiru
git clone https://github.com/NibiruChain/nibiru.git
cd nibiru
git checkout v1.5.0
make install
sudo systemctl restart nibid && sudo journalctl -u nibid -f --no-hostname -o cat
```

**Auto**

```
cd $HOME && \
rm -rf nibiru && \
git clone https://github.com/NibiruChain/nibiru.git && \
cd nibiru && \
git checkout  v1.5.0 && \
make build && \
old_bin_path=$(which nibid) && \
home_path=$HOME && \
rpc_port=$(grep -m 1 -oP '^laddr = "\K[^"]+' "$HOME/.nibid/config/config.toml" | cut -d ':' -f 3) && \
tmux new -s nibiru-upgrade "sudo bash -c 'curl -s https://raw.githubusercontent.com/appieasahbie/testnet-guides/main/utils/autoupgrade/upgrade.sh | bash -s -- -u \"8375044\" -b nibid -n \"$HOME/nibiru/build/nibid\" -o \"$old_bin_path\" -h \"$home_path\" -p \"undefined\" -r \"$rpc_port\"'"
```

