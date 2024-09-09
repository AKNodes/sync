# Sync

## Snapshot

Update every 6 hours

```
sudo systemctl stop axoned

cp $HOME/.axoned/data/priv_validator_state.json $HOME/.axoned/priv_validator_state.json.backup 

axoned tendermint unsafe-reset-all --home $HOME/.axoned --keep-addr-book 
curl https://snapshots.aknodes.net/snapshots/axone/snapshot-axone.AKNodes.lz4 | lz4 -dc - | tar -xf - -C $HOME/.axoned

mv $HOME/.axoned/priv_validator_state.json.backup $HOME/.axoned/data/priv_validator_state.json 

sudo systemctl restart axoned
sudo journalctl -u axoned -f --no-hostname -o cat
```

### State-Sync

```
sudo systemctl stop axoned

cp $HOME/.axoned/data/priv_validator_state.json $HOME/.axoned/priv_validator_state.json.backup
axoned tendermint unsafe-reset-all --home $HOME/.axoned

peers="5edb78aa26a593a77a372079593617809f1ca8fd@167.235.14.83:62656"  
SNAP_RPC="https://rpc.axone.aknodes.net:443"

sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.axoned/config/config.toml 

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height);
BLOCK_HEIGHT=$((LATEST_HEIGHT - 1000));
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash) 

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH && sleep 2

sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ;
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ;
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ;
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ;
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.axoned/config/config.toml

mv $HOME/.axoned/priv_validator_state.json.backup $HOME/.axoned/data/priv_validator_state.json

sudo systemctl restart axoned && sudo journalctl -u axoned -f
```
