# Sync

## Snapshot

```
sudo systemctl stop saod

cp $HOME/.sao/data/priv_validator_state.json $HOME/.sao/priv_validator_state.json.backup 

saod tendermint unsafe-reset-all --home $HOME/.sao --keep-addr-book 
curl https://snapshots.aknodes.net/snapshots/sao/snapshot-saod.AKNodes.lz4 | lz4 -dc - | tar -xf - -C $HOME/.sao

mv $HOME/.sao/priv_validator_state.json.backup $HOME/.sao/data/priv_validator_state.json 

sudo systemctl restart saod
sudo journalctl -u saod -f --no-hostname -o cat
```

### State-Sync

```
sudo systemctl stop saod

cp $HOME/.sao/data/priv_validator_state.json $HOME/.sao/priv_validator_state.json.backup
saod tendermint unsafe-reset-all --home $HOME/.sao

peers="3f649c43579759cc525b8aed6f2a4febe66aa0b1@167.235.14.83:49656"  
SNAP_RPC="https://rpc.sao.aknodes.net:443"

sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.sao/config/config.toml 

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height);
BLOCK_HEIGHT=$((LATEST_HEIGHT - 1000));
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash) 

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH && sleep 2

sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ;
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ;
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ;
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ;
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.mantrachain/config/config.toml

mv $HOME/.mantrachain/priv_validator_state.json.backup $HOME/.sao/data/priv_validator_state.json

sudo systemctl restart saod && sudo journalctl -u saod -f
```
