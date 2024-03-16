# Sync

## Snapshot



```
sudo systemctl stop andromedad

cp $HOME/.andromedad/data/priv_validator_state.json $HOME/.andromedad/priv_validator_state.json.backup 

andromedad tendermint unsafe-reset-all --home $HOME/.andromedad --keep-addr-book 
curl https://snapshots.aknodes.net/snapshots/andromeda/snapshot-andromeda.AKNodes.lz4 | lz4 -dc - | tar -xf - -C $HOME/.andromedad

mv $HOME/.andromedad/priv_validator_state.json.backup $HOME/.andromedad/data/priv_validator_state.json 

sudo systemctl restart andromedad
sudo journalctl -u andromedad -f --no-hostname -o cat
```



### State-Sync



```
sudo systemctl stop andromedad

cp $HOME/.andromedad/data/priv_validator_state.json $HOME/.andromedad/priv_validator_state.json.backup
andromedad tendermint unsafe-reset-all --home $HOME/.andromedad

peers="0b0c0c51a9a0bf170a83431a0f18262dce9e7179@167.235.14.83:33656,e61f287d51edab6f6dbe00a8b804614443ee6f82@80.85.242.117:26656,c5e98925ccca37f07b8ea03d9b7bcfe89834d7fd@94.190.90.38:35656,b99657df20590461b7df5492f97dc46f37082ecd@65.109.101.53:17556,50ca8e25cf1c5a83aa4c79bb1eabfe88b20eb367@65.108.199.120:61356,815e9378b05a40e4a774223b55f5c6b8457a1c79@31.220.79.166:26656,f370dbff00890cb017bef55128aa954c13cac1b2@62.171.153.45:11656"  
SNAP_RPC="https://rpc.andromeda.aknodes.net:443"

sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.andromedad/config/config.toml 

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height);
BLOCK_HEIGHT=$((LATEST_HEIGHT - 1000));
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash) 

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH && sleep 2

sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ;
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ;
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ;
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ;
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.andromedad/config/config.toml

mv $HOME/.andromedad/priv_validator_state.json.backup $HOME/.andromedad/data/priv_validator_state.json

sudo systemctl restart andromedad && sudo journalctl -u andromedad -f
```
