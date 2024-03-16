# Sync

## Snapshot



```
sudo systemctl stop dorad

cp $HOME/.dora/data/priv_validator_state.json $HOME/.dora/priv_validator_state.json.backup 

dorad tendermint unsafe-reset-all --home $HOME/.dora --keep-addr-book 
curl https://snapshots.aknodes.net/snapshots/dora/snapshot-dora.AKNodes.lz4 | lz4 -dc - | tar -xf - -C $HOME/.dora

mv $HOME/.dora/priv_validator_state.json.backup $HOME/.dora/data/priv_validator_state.json 

sudo systemctl restart dorad
sudo journalctl -u dorad -f --no-hostname -o cat
```



### State-Sync



```
sudo systemctl stop dorad

cp $HOME/.dora/data/priv_validator_state.json $HOME/.dora/priv_validator_state.json.backup
dorad tendermint unsafe-reset-all --home $HOME/.dora

peers="3306e10f1635f71e1d93219a369f4907ec062ad5@167.235.14.83:17656,3693ea5a8a9c0590440a7d6c9a98a022ce3b2455@lava-testnet-peer.itrocket.net:20656,0516c4d11552b334a683bdb4410fa22ef7e3f8ba@65.21.239.60:11656,e0f25590f8074b4ea70f069f9be332b19f81f344@23.88.70.109:15656,1f7e104872a3f4259343b8123f333b5b24869b02@159.89.111.21:26656"  
SNAP_RPC="https://rpc.dora.aknodes.net:443"

sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.dora/config/config.toml 

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height);
BLOCK_HEIGHT=$((LATEST_HEIGHT - 1000));
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash) 

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH && sleep 2

sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ;
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ;
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ;
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ;
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.lava/config/config.toml

mv $HOME/.dora/priv_validator_state.json.backup $HOME/.dora/data/priv_validator_state.json

sudo systemctl restart dorad && sudo journalctl -u dorad -f
```
