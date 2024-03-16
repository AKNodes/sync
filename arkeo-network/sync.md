# Sync

## Snapshot



```
sudo systemctl stop arkeod

cp $HOME/.arkeo/data/priv_validator_state.json $HOME/.arkeo/priv_validator_state.json.backup 

arkeod tendermint unsafe-reset-all --home $HOME/.arkeo --keep-addr-book 
curl https://snapshots.aknodes.net/snapshots/arkeo/snapshot-arkeo.AKNodes.lz4 | lz4 -dc - | tar -xf - -C $HOME/.arkeo

mv $HOME/.arkeo/priv_validator_state.json.backup $HOME/.arkeo/data/priv_validator_state.json 

sudo systemctl restart arkeod
sudo journalctl -u arkeod -f --no-hostname -o cat
```



### State-Sync



```
sudo systemctl stop arkeod

cp $HOME/.arkeo/data/priv_validator_state.json $HOME/.arkeo/priv_validator_state.json.backup
arkeod tendermint unsafe-reset-all --home $HOME/.arkeo

peers="844c7132a9f4550bf619edb5f59c20789357748b@167.235.14.83:18656,769de3fabb66d2dcbae7550ce7252f6f469c5d3f@65.108.126.188:26856,e033753cac027fc6605a95dab3b3fc5550d4b9bf@65.109.84.33:40656,2e447e582a3dcd681859acca930034ac684f7f86@158.220.105.59:26656,25a9af68f987e254e50d6d7e6a1e68a5a40c1b7c@65.109.92.148:60556,6ae2136893a08a412f0c02eab8d595d502cd5457@65.108.206.118:36656,5b697eefafeee7e6b907b1512efd5956bd0d700a@167.86.77.84:26656,be71f456a7aa3da953db899298b53d28b75f4676@65.108.229.93:37656,893a44b8501faa22fbe2f4d61c6586f231bd1638@65.109.28.177:33656,804b5d44906ffd71383a8763103ab250dab3c3a7@217.29.57.187:26656,8c2d799bcc4fbf44ef34bbd2631db5c3f4619e41@213.239.207.175:60656"  
SNAP_RPC="https://rpc.arkeo.aknodes.net:443"

sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.arkeo/config/config.toml 

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height);
BLOCK_HEIGHT=$((LATEST_HEIGHT - 1000));
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash) 

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH && sleep 2

sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ;
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ;
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ;
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ;
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.arkeo/config/config.toml

mv $HOME/.arkeo/priv_validator_state.json.backup $HOME/.arkeo/data/priv_validator_state.json

sudo systemctl restart arkeod && sudo journalctl -u arkeod -f
```
