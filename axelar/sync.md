# Sync

## Snapshot



```
sudo systemctl stop axelard

cp $HOME/.axelar/data/priv_validator_state.json $HOME/.axelar/priv_validator_state.json.backup 

axelard tendermint unsafe-reset-all --home $HOME/.axelar --keep-addr-book 
curl https://snapshots.aknodes.net/snapshots/axelar-testnet/snapshot-axelar-testnet.AKNodes.lz4 | lz4 -dc - | tar -xf - -C $HOME/.axelar

mv $HOME/.axelar/priv_validator_state.json.backup $HOME/.axelar/data/priv_validator_state.json 

sudo systemctl restart axelard
sudo journalctl -u axelard -f --no-hostname -o cat
```



### State-Sync



```
#!/bin/bash

SNAP_RPC="https://rpc.axelar.testnet.aknodes.net:443"

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 2000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)

sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"|" $HOME/.axelar/config/config.toml


```



```
chmod 700 state_sync.sh
./state_sync.sh
```

```
systemctl stop axelard
```

```
axelard tendermint unsafe-reset-all --home $HOME/.axelar --keep-addr-book
```

```
sudo systemctl restart axelard && sudo journalctl -u axelard -f
```
