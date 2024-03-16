# Sync

## Snapshot

```
sudo systemctl stop terpd

cp $HOME/.terp/data/priv_validator_state.json $HOME/.terp/priv_validator_state.json.backup 

terpd tendermint unsafe-reset-all --home $HOME/.terp --keep-addr-book 
curl https://snapshots.aknodes.net/snapshots/terp/snapshot-terp.AKNodes.lz4 | lz4 -dc - | tar -xf - -C $HOME/.terp

mv $HOME/.terp/priv_validator_state.json.backup $HOME/.terp/data/priv_validator_state.json 

sudo systemctl restart terpd
sudo journalctl -u terpd -f --no-hostname -o cat
```

### State-Sync

```
sudo systemctl stop terpd

cp $HOME/.terp/data/priv_validator_state.json $HOME/.terp/priv_validator_state.json.backup
terpd tendermint unsafe-reset-all --home $HOME/.terp

peers="ad70324582aa35bf5f727d2e1e3894635b32b4c7@167.235.14.83:47656,f2a287d65f0b20516ab1440514f5820177e2608d@65.109.89.18:13656,b2a4162950e51a2d30d6a08b60acdeace7d911d3@95.217.198.186:30656,9bc20c0878b846cfb180ff40278bb02460b50c7f@65.109.154.181:25656,439f7a680cc645d888317cd64f9b8a6949de394b@65.109.154.185:26656,ad70324582aa35bf5f727d2e1e3894635b32b4c7@167.235.14.83:19656,da9a83ef835387e3813bd5cd79b1b0193f522d7c@65.21.152.68:26656,3437e99cbc0dc272071dd6a9098ef4c5459a6805@65.108.68.86:10456,06a68cd28f6b57768c950af7f2ba37b4d8bd7f5e@142.132.248.253:65532"  
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
