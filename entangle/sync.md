# Sync

## Snapshot

<pre><code><strong>sudo systemctl stop entangled
</strong><strong>
</strong>cp $HOME/.entangled/data/priv_validator_state.json $HOME/.entangled/priv_validator_state.json.backup 
entangled tendermint unsafe-reset-all --home $HOME/.entangled --keep-addr-book
rm -rf $HOME/.entangled/data 

curl https://snapshots.aknodes.net/snapshots/entangle/snapshot-entangle.AKNodes.lz4 | lz4 -dc - | tar -xf - -C $HOME/.entangled
mv $HOME/.entangled/priv_validator_state.json.backup $HOME/.entangled/data/priv_validator_state.json 

sudo systemctl restart entangled
sudo journalctl -u entangled -f --no-hostname -o cat
</code></pre>

### State-Sync

```
sudo systemctl stop entangled

cp $HOME/.entangled/data/priv_validator_state.json $HOME/.entangled/priv_validator_state.json.backup
entangled tendermint unsafe-reset-all --home $HOME/.entangled

peers="741cb77bbbea6c2ec1a5b343be5f9144f9ca7e08@167.235.14.83:14656,3137cb72e3ff5f479600dcbd2f2c9e17a83f8740@65.109.82.17:12356,fd5198a2631443b04ea0cc579007a3dabc413fa6@109.205.181.105:26656,3cda1bd5ff6a5cdf7a7375cb66d3a7fcb15ae811@146.190.214.226:26656,fb9f3b489ab814fb12cc4fa2634bfd5f1dc570b6@37.120.191.47:60656,741cb77bbbea6c2ec1a5b343be5f9144f9ca7e08@167.235.14.83:14656,65b88f9792a216c39189f1ec7cacf11d99388ce8@65.108.229.93:25656,8ca8ea0ab34634657b69436e6b16a050257d0643@167.235.117.3:36656,60df860c2fc6ae81e7c62f334823768a5135cbba@142.132.194.170:22656,67eca0ca25a05e2508019224e92613bfd5ed0643@144.126.219.151:20356,79e8a7cba886e98da16e2e04639f62f39a10c592@147.182.190.14:26656"  
SNAP_RPC="https://rpc.entangle.aknodes.net:443"

sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.entangled/config/config.toml 

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height);
BLOCK_HEIGHT=$((LATEST_HEIGHT - 1000));
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash) 

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH && sleep 2

sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ;
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ;
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ;
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ;
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.entangled/config/config.toml

mv $HOME/.entangled/priv_validator_state.json.backup $HOME/.entangled/data/priv_validator_state.json

sudo systemctl restart entangled && sudo journalctl -u entangled -f
```
