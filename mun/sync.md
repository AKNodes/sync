# Sync

## Snapshot

```
sudo systemctl stop mund

cp $HOME/.mun/data/priv_validator_state.json $HOME/.mun/priv_validator_state.json.backup 

mund tendermint unsafe-reset-all --home $HOME/.mun --keep-addr-book 
curl https://snapshots.aknodes.net/snapshots/mun-testnet/snapshot-mun-testnet.AKNodes.lz4 | lz4 -dc - | tar -xf - -C $HOME/.mun

mv $HOME/.mun/priv_validator_state.json.backup $HOME/.mun/data/priv_validator_state.json 

sudo systemctl restart mund
sudo journalctl -u mund -f --no-hostname -o cat
```

### State-Sync

```
sudo systemctl stop mund

cp $HOME/.mun/data/priv_validator_state.json $HOME/.mun/priv_validator_state.json.backup
mund tendermint unsafe-reset-all --home $HOME/.mun

peers="51b2349d68ce202631e607f9de19003bfa2bc40c@167.235.14.83:37656"  
SNAP_RPC="https://rpc.mun.aknodes.net:443"

sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.mun/config/config.toml 

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height);
BLOCK_HEIGHT=$((LATEST_HEIGHT - 1000));
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash) 

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH && sleep 2

sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ;
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ;
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ;
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ;
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.terp/config/config.toml

mv $HOME/.mun/priv_validator_state.json.backup $HOME/.mun/data/priv_validator_state.json

sudo systemctl restart mund && sudo journalctl -u mund -f
```
