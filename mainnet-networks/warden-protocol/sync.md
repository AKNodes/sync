# Sync

## Snapshot

Update every 6 hours

```
sudo systemctl stop wardend

cp $HOME/.warden/data/priv_validator_state.json $HOME/.warden/priv_validator_state.json.backup 

wardend tendermint unsafe-reset-all --home $HOME/.warden --keep-addr-book 
curl https://snapshots.aknodes.net/snapshots/warden-mainnet/snapshot-warden-mainnet.AKNodes.lz4 | lz4 -dc - | tar -xf - -C $HOME/.warden

mv $HOME/.warden/priv_validator_state.json.backup $HOME/.warden/data/priv_validator_state.json 

sudo systemctl restart wardend
sudo journalctl -u wardend -f --no-hostname -o cat
```



### State-Sync



<pre><code>sudo systemctl stop wardend

cp $HOME/.warden/data/priv_validator_state.json $HOME/.warden/priv_validator_state.json.backup
wardend tendermint unsafe-reset-all --home $HOME/.warden

<strong>peers="7345881034f09a7b6e621bed6f803168139a9a62@167.235.14.83:13656"  
</strong>SNAP_RPC="https://rpc.warden.aknodes.net:443"

sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.warden/config/config.toml 

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height);
BLOCK_HEIGHT=$((LATEST_HEIGHT - 1000));
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash) 

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH &#x26;&#x26; sleep 2

sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ;
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ;
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ;
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ;
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.warden/config/config.toml

mv $HOME/.warden/priv_validator_state.json.backup $HOME/.warden/data/priv_validator_state.json

sudo systemctl restart wardend &#x26;&#x26; sudo journalctl -u wardend -f
</code></pre>
