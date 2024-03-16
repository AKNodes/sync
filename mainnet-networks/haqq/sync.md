# Sync

## Snapshot



```
sudo systemctl stop haqqd

cp $HOME/.haqqd/data/priv_validator_state.json $HOME/.haqqd/priv_validator_state.json.backup 

haqqd tendermint unsafe-reset-all --home $HOME/.haqqd --keep-addr-book 
curl https://snapshots.aknodes.net/snapshots/haqq/snapshot-haqq.AKNodes.lz4 | lz4 -dc - | tar -xf - -C $HOME/.haqqd

mv $HOME/.haqqd/priv_validator_state.json.backup $HOME/.haqqd/data/priv_validator_state.json 

sudo systemctl restart haqqd
sudo journalctl -u haqqd -f --no-hostname -o cat
```



### State-Sync



```
sudo systemctl stop haqqd

cp $HOME/.haqqd/data/priv_validator_state.json $HOME/.haqqd/priv_validator_state.json.backup
haqqd tendermint unsafe-reset-all --home $HOME/.haqqd

peers="9e05eb2af83ee4071989650256219d136c4d2817@167.235.14.83:19656,9ab487cfdc51465fc268f0426ac669828c3664be@95.217.57.232:60656,8625047e1858c7ca78479cd78fe74b30b8ba57ec@3.78.97.208:26656,e345b6d6db937518aca00afb91c9e63e55b4fac1@135.181.183.212:26656,d3df08f26b7ab5fc3c18deba99a596c044d68698@188.34.193.133:26656,e04d814cf820c498e64153c27b021be1a70b6f6b@65.109.33.48:25656,861e5892470275a394ebab407e8d62b1931843d3@192.248.181.17:26656,4e1c2471efb89239fb04a4b75f9f87177fd91d00@134.65.195.37:26656,2a39dc69d166e3c62f6e5a3a579294220521609f@65.109.103.177:36656,c124ce0b508e8b9ed1c5b6957f362225659b5343@164.152.160.218:26656,05a801d1906a15c4d5adca5aace3297ab800e6d2@8.209.105.6:26656,9295e4898f83139726f3c5dd87d73efcd328e8f2@148.113.143.220:26656,51dfe789d1616a086649065bad6ce4d6561bc87c@51.159.98.175:26656,a6ef0acf5f2ff05ad4ecb977479aa9d0d10b8da5@34.32.177.0:26656,0533e20e65912f72f2ad88a4c91eefbc634212d7@43.131.29.47:26656,701d1e93c43ac892e7a5e493034114714383373e@65.109.108.47:24056,ee0a4e30c2a8842635b22667644dd4a8d7a02fd5@89.208.146.222:26656,026ec6392ff7cbcdf1910d7fc7b6bc4f589d2b90@5.9.237.193:26656"  
SNAP_RPC="https://rpc.haqq.aknodes.net:443"

sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.haqqd/config/config.toml 

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height);
BLOCK_HEIGHT=$((LATEST_HEIGHT - 1000));
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash) 

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH && sleep 2

sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ;
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ;
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ;
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ;
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.haqqd/config/config.toml

mv $HOME/.haqqd/priv_validator_state.json.backup $HOME/.haqqd/data/priv_validator_state.json

sudo systemctl restart haqqd && sudo journalctl -u haqqd -f
```
