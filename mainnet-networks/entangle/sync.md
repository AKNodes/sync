# Sync

## Snapshot

Update every 6 hours

<pre><code><strong>sudo systemctl stop entangled
</strong><strong>
</strong>cp $HOME/.entangled/data/priv_validator_state.json $HOME/.entangled/priv_validator_state.json.backup 
entangled tendermint unsafe-reset-all --home $HOME/.entangled --keep-addr-book
rm -rf $HOME/.entangled/data 

curl https://snapshots.aknodes.net/snapshots/entangle/snapshot-entangle.AKNodes.lz4 | lz4 -dc - | tar -xf - -C $HOME/.entangled
mv $HOME/.entangled/priv_validator_state.json.backup $HOME/.entangled/data/priv_validator_state.json 

sudo systemctl restart entangled
sudo journalctl -u entangled -f --no-hostname -o cat
</code></pre>

### State-Sync



```
sudo systemctl stop entangled

cp $HOME/.entangled/data/priv_validator_state.json $HOME/.entangled/priv_validator_state.json.backup
entangled tendermint unsafe-reset-all --home $HOME/.entangled

peers="17ee5be69ac4bd7425ae262b12760ef5953ce4f0@167.235.14.83:14456"  
SNAP_RPC="https://rpc.entangle.aknodes.net:443"

sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.entangled/config/config.toml 

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height);
BLOCK_HEIGHT=$((LATEST_HEIGHT - 1000));
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash) 

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH && sleep 2

sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ;
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ;
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ;
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ;
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.entangled/config/config.toml

mv $HOME/.entangled/priv_validator_state.json.backup $HOME/.entangled/data/priv_validator_state.json

sudo systemctl restart entangled && sudo journalctl -u entangled -f
```
