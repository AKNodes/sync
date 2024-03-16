# Sync

## Snapshot



```
sudo systemctl stop hedged

cp $HOME/.hedge/data/priv_validator_state.json $HOME/.hedge/priv_validator_state.json.backup 

hedged tendermint unsafe-reset-all --home $HOME/.hedge --keep-addr-book 
curl https://snapshots.aknodes.net/snapshots/hedge/snapshot-hedge.AKNodes.lz4 | lz4 -dc - | tar -xf - -C $HOME/.hedge

mv $HOME/.hedge/priv_validator_state.json.backup $HOME/.hedge/data/priv_validator_state.json 

sudo systemctl restart hedged
sudo journalctl -u hedged -f --no-hostname -o cat
```



### State-Sync



```
sudo systemctl stop hedged

cp $HOME/.hedge/data/priv_validator_state.json $HOME/.hedge/priv_validator_state.json.backup
hedged tendermint unsafe-reset-all --home $HOME/.hedge

peers="8ac6567595653de3ee0b37d390e26e84fffd05ec@167.235.14.83:34656"  
SNAP_RPC="https://rpc.hedge.aknodes.net:443"

sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.hedge/config/config.toml 

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height);
BLOCK_HEIGHT=$((LATEST_HEIGHT - 1000));
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash) 

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH && sleep 2

sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ;
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ;
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ;
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ;
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.hedge/config/config.toml

mv $HOME/.hedge/priv_validator_state.json.backup $HOME/.hedge/data/priv_validator_state.json

sudo systemctl restart hedged && sudo journalctl -u hedged -f
```
