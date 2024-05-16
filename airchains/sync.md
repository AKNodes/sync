# Sync

### Snapshot <a href="#snapshot" id="snapshot"></a>

Update every 6 hours

```
sudo systemctl stop junctiond

cp $HOME/.junction/data/priv_validator_state.json $HOME/.junction/priv_validator_state.json.backup 

junctiond tendermint unsafe-reset-all --home $HOME/.junction --keep-addr-book 
curl https://snapshots.aknodes.net/snapshots/airchains/snapshot-airchains.AKNodes.lz4 | lz4 -dc - | tar -xf - -C $HOME/.junction

mv $HOME/.junction/priv_validator_state.json.backup $HOME/.junction/data/priv_validator_state.json 

sudo systemctl restart junctiond
sudo journalctl -u junctiond -f --no-hostname -o cat
```

#### &#x20;<a href="#state-sync" id="state-sync"></a>

#### State-Sync <a href="#state-sync" id="state-sync"></a>

```
sudo systemctl stop junctiond

cp $HOME/.junction/data/priv_validator_state.json $HOME/.junction/priv_validator_state.json.backup
junctiond tendermint unsafe-reset-all --home $HOME/.lava

peers="9f785525827dc317d16a66605195cf5dfbe7276d@167.235.14.83:34656"  
SNAP_RPC="https://rpc.junction.aknodes.net:443"

sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.junction/config/config.toml 

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height);
BLOCK_HEIGHT=$((LATEST_HEIGHT - 1000));
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash) 

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH && sleep 2

sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ;
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ;
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ;
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ;
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.junction/config/config.toml

mv $HOME/.junction/priv_validator_state.json.backup $HOME/.junction/data/priv_validator_state.json

sudo systemctl restart junctiond && sudo journalctl -u junctiond -f
```
