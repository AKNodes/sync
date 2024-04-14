# Sync

## Snapshot

Update every 6 hours

```
sudo systemctl stop galacticad

cp $HOME/.galactica/data/priv_validator_state.json $HOME/.galactica/priv_validator_state.json.backup 

galacticad tendermint unsafe-reset-all --home $HOME/.galactica --keep-addr-book 
curl https://snapshots.aknodes.net/snapshots/galactica/snapshot-galactica.AKNodes.lz4 | lz4 -dc - | tar -xf - -C $HOME/.galactica

mv $HOME/.galactica/priv_validator_state.json.backup $HOME/.galactica/data/priv_validator_state.json 

sudo systemctl restart galacticad
sudo journalctl -u galacticad -f --no-hostname -o cat
```



### State-Sync



```
sudo systemctl stop galacticad

cp $HOME/.galactica/data/priv_validator_state.json $HOME/.galactica/priv_validator_state.json.backup
galacticad tendermint unsafe-reset-all --home $HOME/.galactica

peers="03d3c773e52c46274e35f3a36a7644e7f2ff37be@167.235.14.83:35656"  
SNAP_RPC="https://rpc.galactica.aknodes.net:443"

sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.galactica/config/config.toml 

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height);
BLOCK_HEIGHT=$((LATEST_HEIGHT - 1000));
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash) 

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH && sleep 2

sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ;
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ;
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ;
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ;
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.side/config/config.toml

mv $HOME/.galactica/priv_validator_state.json.backup $HOME/.galactica/data/priv_validator_state.json

sudo systemctl restart galacticad && sudo journalctl -u galacticad -f
```
