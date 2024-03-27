# Sync

## Snapshot



```
sudo systemctl stop sided

cp $HOME/.side/data/priv_validator_state.json $HOME/.side/priv_validator_state.json.backup 

sided tendermint unsafe-reset-all --home $HOME/.side --keep-addr-book 
curl https://snapshots.aknodes.net/snapshots/side/snapshot-side.AKNodes.lz4 | lz4 -dc - | tar -xf - -C $HOME/.side

mv $HOME/.side/priv_validator_state.json.backup $HOME/.side/data/priv_validator_state.json 

sudo systemctl restart sided
sudo journalctl -u sided -f --no-hostname -o cat
```



### State-Sync



```
sudo systemctl stop sided

cp $HOME/.side/data/priv_validator_state.json $HOME/.side/priv_validator_state.json.backup
sided tendermint unsafe-reset-all --home $HOME/.side

peers="1688ce2c799e2ea5f73fb017895ea6fb5d4a5f14@167.235.14.83:30656"  
SNAP_RPC="https://rpc.side.aknodes.net:443"

sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.side/config/config.toml 

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height);
BLOCK_HEIGHT=$((LATEST_HEIGHT - 1000));
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash) 

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH && sleep 2

sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ;
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ;
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ;
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ;
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.side/config/config.toml

mv $HOME/.side/priv_validator_state.json.backup $HOME/.side/data/priv_validator_state.json

sudo systemctl restart sided && sudo journalctl -u sided -f
```
