# Sync

## Snapshot

Update every 6 hours

```
sudo systemctl stop nibid

cp $HOME/.nibid/data/priv_validator_state.json $HOME/.nibid/priv_validator_state.json.backup 

nibid tendermint unsafe-reset-all --home $HOME/.nibid --keep-addr-book 
curl https://snapshots.aknodes.net/snapshots/nibiru-mainnet/snapshot-nibiru-mainnet.AKNodes.lz4 | lz4 -dc - | tar -xf - -C $HOME/.nibid

mv $HOME/.nibid/priv_validator_state.json.backup $HOME/.nibid/data/priv_validator_state.json 

sudo systemctl restart nibid
sudo journalctl -u nibid -f --no-hostname -o cat
```



### State-Sync



```
sudo systemctl stop nibid

cp $HOME/.nibid/data/priv_validator_state.json $HOME/.nibid/priv_validator_state.json.backup
nibid tendermint unsafe-reset-all --home $HOME/.nibid

peers="ccdaa4db971cb4c428e33add35a4db58b96ebbad@167.235.14.83:61656"  
SNAP_RPC="https://rpc.nibiru-mainnet.aknodes.net:443"

sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.nibid/config/config.toml 

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height);
BLOCK_HEIGHT=$((LATEST_HEIGHT - 1000));
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash) 

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH && sleep 2

sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ;
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ;
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ;
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ;
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.nibid/config/config.toml

mv $HOME/.nibid/priv_validator_state.json.backup $HOME/.nibid/data/priv_validator_state.json

sudo systemctl restart nibid && sudo journalctl -u nibid -f
```



### Pricefeeder upgrade&#x20;

```
systemctl stop pricefeeder.service

curl -s https://get.nibiru.fi/pricefeeder@v1.0.2! | bash

systemctl restart pricefeeder.service && journalctl -u pricefeeder.service -f
```
