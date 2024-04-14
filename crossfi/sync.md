# Sync

## Snapshot

Update every 6 hours

```
sudo systemctl stop crossfid

cp $HOME/.crossfi/data/priv_validator_state.json $HOME/.crossfi/priv_validator_state.json.backup 

crossfid tendermint unsafe-reset-all --home $HOME/.crossfi --keep-addr-book 
curl https://snapshots.aknodes.net/snapshots/crossfi/snapshot-crossfi.AKNodes.lz4 | lz4 -dc - | tar -xf - -C $HOME/.crossfi

mv $HOME/.crossfi/priv_validator_state.json.backup $HOME/.crossfi/data/priv_validator_state.json 

sudo systemctl restart crossfid
sudo journalctl -u crossfid -f --no-hostname -o cat
```



### State-Sync



```
sudo systemctl stop crossfid

cp $HOME/.crossfi/data/priv_validator_state.json $HOME/.crossfi/priv_validator_state.json.backup
crossfid tendermint unsafe-reset-all --home $HOME/.crossfi

peers="9d6330bb966e1475082cda63102ed4a707cf8fdf@148.113.153.62:26656"  
SNAP_RPC="https://rpc.crossfi.aknodes.net:443"

sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.crossfi/config/config.toml 

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height);
BLOCK_HEIGHT=$((LATEST_HEIGHT - 1000));
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash) 

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH && sleep 2

sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ;
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ;
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ;
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ;
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.crossfi/config/config.toml

mv $HOME/.crossfi/priv_validator_state.json.backup $HOME/.crossfi/data/priv_validator_state.json

sudo systemctl restart crossfid && sudo journalctl -u crossfid -f
```
