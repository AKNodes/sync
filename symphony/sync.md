# Sync

### Snapshot <a href="#snapshot" id="snapshot"></a>

Update every 6 hours

```
sudo systemctl stop symphonyd

cp $HOME/.symphonyd/data/priv_validator_state.json $HOME/.symphonyd/priv_validator_state.json.backup 

symphonyd tendermint unsafe-reset-all --home $HOME/.symphonyd --keep-addr-book 
curl https://snapshots.aknodes.net/snapshots/symphony/snapshot-symphony.AKNodes.lz4 | lz4 -dc - | tar -xf - -C $HOME/.symphonyd

mv $HOME/.symphonyd/priv_validator_state.json.backup $HOME/.symphonyd/data/priv_validator_state.json 

sudo systemctl restart symphonyd
sudo journalctl -u symphonyd -f --no-hostname -o cat
```

#### &#x20;<a href="#state-sync" id="state-sync"></a>

#### State-Sync <a href="#state-sync" id="state-sync"></a>

```
sudo systemctl stop symphonyd

cp $HOME/.symphonyd/data/priv_validator_state.json $HOME/.symphonyd/priv_validator_state.json.backup
symphonyd tendermint unsafe-reset-all --home $HOME/.symphonyd

peers="90b8e752f6b60977a68eeb13b6fcdce22103702c@148.113.153.62:29656"  
SNAP_RPC="https://rpc.symphony.aknodes.net:443"

sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.symphonyd/config/config.toml 

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height);
BLOCK_HEIGHT=$((LATEST_HEIGHT - 1000));
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash) 

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH && sleep 2

sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ;
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ;
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ;
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ;
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.symphonyd/config/config.toml

mv $HOME/.symphonyd/priv_validator_state.json.backup $HOME/.symphonyd/data/priv_validator_state.json

sudo systemctl restart symphonyd && sudo journalctl -u symphonyd -f
```
