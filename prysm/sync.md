# Sync

### Snapshot <a href="#snapshot" id="snapshot"></a>

Update every 6 hours

```
sudo systemctl stop prysmd

cp $HOME/.prysm/data/priv_validator_state.json $HOME/.prysm/priv_validator_state.json.backup 

prysmd tendermint unsafe-reset-all --home $HOME/.prysm --keep-addr-book 
curl https://snapshots.aknodes.net/snapshots/prysm/snapshot-prysm.AKNodes.lz4 | lz4 -dc - | tar -xf - -C $HOME/.prysm

mv $HOME/.prysm/priv_validator_state.json.backup $HOME/.prysm/data/priv_validator_state.json 

sudo systemctl restart prysmd
sudo journalctl -u prysmd -f --no-hostname -o cat
```

#### State-Sync <a href="#state-sync" id="state-sync"></a>

```
sudo systemctl stop prysmd

cp $HOME/.prysm/data/priv_validator_state.json $HOME/.prysm/priv_validator_state.json.backup
prysmd tendermint unsafe-reset-all --home $HOME/.prysm

peers="10df1086c5e317b3d7d67323a2cee9a9ccd19c9e@167.235.14.83:11656"  
SNAP_RPC="https://rpc.prysm.aknodes.net:443"

sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.prysm/config/config.toml 

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height);
BLOCK_HEIGHT=$((LATEST_HEIGHT - 1000));
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash) 

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH && sleep 2

sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ;
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ;
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ;
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ;
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.prysm/config/config.toml

mv $HOME/.prysm/priv_validator_state.json.backup $HOME/.prysm/data/priv_validator_state.json

sudo systemctl restart prysmd && sudo journalctl -u prysmd -f
```
