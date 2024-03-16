# Sync

## Snapshot

```
sudo systemctl stop terpd

cp $HOME/.terp/data/priv_validator_state.json $HOME/.terp/priv_validator_state.json.backup 

terpd tendermint unsafe-reset-all --home $HOME/.terp --keep-addr-book 
curl https://snapshots.aknodes.net/snapshots/terp-testnet/snapshot-terp-testnet.AKNodes.lz4 | lz4 -dc - | tar -xf - -C $HOME/.terp

mv $HOME/.terp/priv_validator_state.json.backup $HOME/.terp/data/priv_validator_state.json 

sudo systemctl restart terpd
sudo journalctl -u terpd -f --no-hostname -o cat
```

### State-Sync

```
sudo systemctl stop terpd

cp $HOME/.terp/data/priv_validator_state.json $HOME/.terp/priv_validator_state.json.backup
terpd tendermint unsafe-reset-all --home $HOME/.terp

peers="e23c7ecc1c07bcf0f6a17f8fe09a88adffc1dd89@167.235.14.83:13656,2f0f98eb3965cc9949073b1f0e75a5e55be44ed2@65.109.28.177:21856,e23c7ecc1c07bcf0f6a17f8fe09a88adffc1dd89@167.235.14.83:13656"  
SNAP_RPC="https://rpc.terp.aknodes.net:443"

sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.terp/config/config.toml 

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height);
BLOCK_HEIGHT=$((LATEST_HEIGHT - 1000));
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash) 

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH && sleep 2

sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ;
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ;
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ;
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ;
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.terp/config/config.toml

mv $HOME/.terp/priv_validator_state.json.backup $HOME/.terp/data/priv_validator_state.json

sudo systemctl restart terpd && sudo journalctl -u terpd -f
```
