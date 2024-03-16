# Sync

## Snapshot

```
sudo systemctl stop mantrachaind

cp $HOME/.mantrachain/data/priv_validator_state.json $HOME/.mantrachain/priv_validator_state.json.backup 

mantrachaind tendermint unsafe-reset-all --home $HOME/.mantrachain --keep-addr-book 
curl https://snapshots.aknodes.net/snapshots/mantrachain/snapshot-mantrachain.AKNodes.lz4 | lz4 -dc - | tar -xf - -C $HOME/.mantrachain

mv $HOME/.mantrachain/priv_validator_state.json.backup $HOME/.mantrachain/data/priv_validator_state.json 

sudo systemctl restart mantrachaind
sudo journalctl -u mantrachaind -f --no-hostname -o cat
```

### State-Sync

```
sudo systemctl stop mantrachaind

cp $HOME/.entangled/data/priv_validator_state.json $HOME/.entangled/priv_validator_state.json.backup
mantrachaind tendermint unsafe-reset-all --home $HOME/.mantrachain

peers="63763bfb78d296187754c367a9740e24730a7fc4@167.235.14.83:32656,eea43b2084e78945c3a51babe659efe55fe58370@35.222.198.102:26656,21ff2d5d6c4abcc7c4874ab6bf68551da979656a@172.93.185.122:26656"  
SNAP_RPC="https://rpc.mantrachain.aknodes.net:443"

sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.mantrachain/config/config.toml 

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height);
BLOCK_HEIGHT=$((LATEST_HEIGHT - 1000));
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash) 

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH && sleep 2

sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ;
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ;
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ;
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ;
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.mantrachain/config/config.toml

mv $HOME/.mantrachain/priv_validator_state.json.backup $HOME/.mantrachain/data/priv_validator_state.json

sudo systemctl restart mantrachaind && sudo journalctl -u mantrachaind -f
```
