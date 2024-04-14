# Sync

## Snapshot

Update every 6 hours

```
sudo systemctl stop ojod

cp $HOME/.ojo/data/priv_validator_state.json $HOME/.ojo/priv_validator_state.json.backup 

ojod tendermint unsafe-reset-all --home $HOME/.ojo --keep-addr-book 
curl https://snapshots.aknodes.net/snapshots/ojo/snapshot-ojo.AKNodes.lz4 | lz4 -dc - | tar -xf - -C $HOME/.ojo

mv $HOME/.ojo/priv_validator_state.json.backup $HOME/.ojo/data/priv_validator_state.json 

sudo systemctl restart ojod
sudo journalctl -u ojod -f --no-hostname -o cat
```



### State-Sync



```
sudo systemctl stop ojod

cp $HOME/.ojo/data/priv_validator_state.json $HOME/.ojo/priv_validator_state.json.backup
ojod tendermint unsafe-reset-all --home $HOME/.ojo

peers="a5614b1bbd5028e6df6bc279e71d984bf2699088@167.235.14.83:13956,f1da105838c677cb1e07a7a94cb277cdf24dd2df@91.207.54.57:26656,7664f59f128eb81d7209e33f59adcc5386aac67c@95.217.110.174:26656,2032c0663fb43bc45b69f079131eb1517a3266e4@31.220.75.138:30656,7a1a618b53bd0d16ec044fe21b53c5607abf176d@35.205.157.12:26656,8d800f25d7b8576cfeddf0ab1f2f21474e299486@124.115.207.253:26656,671e90537802858d7bdaa4f9ff0b0cea2323f005@88.198.52.46:19856,b60d74bff60c488bab38168a716ebe369fffb540@123.138.66.167:26656,e7ffd4c351785476aa3b362047759a8cad295da6@95.216.44.225:11656,564cbed0f73137c11620b37e2bf27f8b3f960e21@91.207.54.121:26656,8825b79f5610daea5ce745aed719ef2cbd198878@95.217.39.116:11656,698a8133bb74ca288ee8a45fc4ca950116a7da0e@91.207.54.204:26656,111dd6b7ac9d0f80d7a04ce212267ce95cb913e9@65.21.133.86:26656,60d5e034d23e14821507849a811042000abaee11@207.244.231.52:34656,081ff903784a3f1b69522d6167c998c88c91ce61@65.108.13.154:27656,c8135e557ae09c9f05ff9fca5cc66caf4f7dcb6a@91.207.54.156:26656,ee026a937bbb120e754a5af4cc9417bc0a10642f@36.47.76.171:26656,306b7549a5fd41c77a1695dea306292ffae38d8b@34.140.34.185:26656,76a0e0b05f4d25ca9ff80d9e49b42308f168fe65@91.207.54.81:26656,ac3d95ea70dbb52d2cd7652692430938faa7c3a8@158.220.109.60:26656"  
SNAP_RPC="https://rpc.ojo.aknodes.net:443"

sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.ojo/config/config.toml 

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height);
BLOCK_HEIGHT=$((LATEST_HEIGHT - 1000));
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash) 

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH && sleep 2

sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ;
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ;
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ;
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ;
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.ojo/config/config.toml

mv $HOME/.ojo/priv_validator_state.json.backup $HOME/.ojo/data/priv_validator_state.json

sudo systemctl restart ojod && sudo journalctl -u ojod -f
```
