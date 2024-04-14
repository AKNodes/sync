# Sync

## Snapshot

Update every 6 hours



```
sudo systemctl stop swisstronikd

cp $HOME/.swisstronik/data/priv_validator_state.json $HOME/.swisstronik/priv_validator_state.json.backup 

swisstronikd tendermint unsafe-reset-all --home $HOME/.swisstronik --keep-addr-book 
curl https://snapshots.aknodes.net/snapshots/swisstronik/snapshot-swisstronik.AKNodes.lz4 | lz4 -dc - | tar -xf - -C $HOME/.swisstronik

mv $HOME/.swisstronik/priv_validator_state.json.backup $HOME/.swisstronik/data/priv_validator_state.json 

sudo systemctl restart swisstronikd
sudo journalctl -u swisstronikd -f --no-hostname -o cat
```



### State-Sync



```
sudo systemctl stop swisstronik

cp $HOME/.swisstronik/data/priv_validator_state.json $HOME/.swisstronik/priv_validator_state.json.backup
swisstronikd tendermint unsafe-reset-all --home $HOME/.swisstronik

peers="9d6330bb966e1475082cda63102ed4a707cf8fdf@148.113.153.62:26656"  
SNAP_RPC="https://rpc.swisstronik.aknodes.net:443"

sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.swisstronik/config/config.toml 

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height);
BLOCK_HEIGHT=$((LATEST_HEIGHT - 1000));
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash) 

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH && sleep 2

sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ;
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ;
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ;
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ;
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.swisstronik/config/config.toml

mv $HOME/.swisstronik/priv_validator_state.json.backup $HOME/.swisstronik/data/priv_validator_state.json

sudo systemctl restart swisstronikd && sudo journalctl -u swisstronikd -f
```
