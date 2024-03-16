# Sync

## Snapshot



```
sudo systemctl stop evmosd

cp $HOME/.evmosd/data/priv_validator_state.json $HOME/.evmosd/priv_validator_state.json.backup 

evmosd tendermint unsafe-reset-all --home $HOME/.evmosd --keep-addr-book 
curl https://snapshots.aknodes.net/snapshots/evmos-testnet/snapshot-evmos-testnet.AKNodes.lz4 | lz4 -dc - | tar -xf - -C $HOME/.evmosd

mv $HOME/.evmosd/priv_validator_state.json.backup $HOME/.evmosd/data/priv_validator_state.json 

sudo systemctl restart evmosd
sudo journalctl -u evmosd -f --no-hostname -o cat
```



### State-Sync

```
#!/bin/bash

SNAP_RPC="https://rpc.evmos.testnet.aknodes.net:443"

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 2000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)

sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"|" $HOME/.evmosd/config/config.toml


```



```
chmod 700 state_sync.sh
./state_sync.sh
```

```
systemctl stop evmosd
```

```
evmosd tendermint unsafe-reset-all --home $HOME/.evmosd --keep-addr-book
```

```
sudo systemctl restart evmosd && sudo journalctl -u evmosd -f
```
