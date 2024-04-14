# Sync

## Snapshot

Update every 6 hours

```
sudo systemctl stop okp4d

cp $HOME/.okp4d/data/priv_validator_state.json $HOME/.okp4d/priv_validator_state.json.backup 

okp4d tendermint unsafe-reset-all --home $HOME/.okp4d --keep-addr-book 
curl https://snapshots.aknodes.net/snapshots/okp4/snapshot-okp4.AKNodes.lz4 | lz4 -dc - | tar -xf - -C $HOME/.okp4d

mv $HOME/.okp4d/priv_validator_state.json.backup $HOME/.okp4d/data/priv_validator_state.json 

sudo systemctl restart okp4d
sudo journalctl -u okp4d -f --no-hostname -o cat
```

### State-Sync

```
sudo systemctl stop okp4d

cp $HOME/.okp4d/data/priv_validator_state.json $HOME/.okp4d/priv_validator_state.json.backup
okp4d tendermint unsafe-reset-all --home $HOME/.okp4d

peers="4322de3e20476861b013536ab2806ec85fb47d27@167.235.14.83:26656,61544968b65e34a59513b67613519cd37ace7ecb@161.97.151.109:26656,883442818e3aa98f78818659042a89b3e3165734@213.133.100.172:27363,907afed8fb7561b6d9bc6f755c8e4cce52e55080@65.108.72.252:26656,e8a3d8287832167020ecef24444048f614b5a11c@65.109.69.240:26656,b0c17c74ab96b5422277089d2cfb765edf9f29b1@65.109.84.33:34656,1e48c09a0f78070e90ed49b2e3d59f8fdc188e74@162.55.234.70:55156,5a3640b12914dad5d039e3a53ee3e1a15618965f@65.109.92.249:20621,3ecbc8aa00b5dd8af88af7496489b0054e3b4d7f@138.68.182.247:26656,e755eb8016c2f6f5303b2f8d503d9126d235e80f@138.201.35.56:26656,29d7518c50f1e31dfd5a87c70cff3a3a85cdc9a8@81.30.157.35:10656,dc835ee8a89598e7f9f28cb595a5f4cef363ab04@217.182.220.75:26656,d21f15a5ad8b9bc1b0e0828e8475137a749ae554@173.212.222.177:26656,1f4fa23210cc1d086a928a3c6de7c24f6c8f17ba@202.61.226.120:16656,bd5272b542ca251ff2aeb54241d8ce225628a0d5@148.251.133.248:17656,f7e481df45bfbe62ea0553f5f6da34eaf4f688c3@194.34.232.225:26656,6a66a38bdd5895ec6f1ce18b3430860a30e18e02@142.132.149.118:26656,17f9c2f330400cc1eb7e637a3be6bb0b9e5a9823@167.235.180.97:17656,264256d32511c512a0a9d4098310a057c9999fd1@65.21.90.141:12234,0f0c071ffc1948767fa866501a4d7d3144b8bf0b@95.217.110.39:26256,a49302f8999e5a953ebae431c4dde93479e17155@15.235.46.79:26656,9c462b1c0ba63115bd70c3bd4f2935fcb93721d0@65.21.170.3:42656,a32d9454d6a1fa44462166ef3da7f47e84bdff4d@65.108.71.119:40656,307fb25cd6998d0d5bd1d947571f6043c6bb4069@65.109.31.114:2280,c665d7fd39015a805f1af935293fefdc825a6b6b@185.144.99.16:26656,5c2a752c9b1952dbed075c56c600c3a79b58c395@195.3.220.54:26996,a855bd9e3b689f3d055811161dd851ce1f7aec74@65.108.141.228:26856,3fb43d522df12137fea6da68ea956f650539c0d5@65.109.154.182:17656,f045c5324e03d54f96285a33130d3886457e18be@46.4.81.204:49656,e20b9048c220d4a8b7e7934fddb3c4fb20c20bdd@81.0.246.196:26656,f9a0e04c04d4b0ec6f7e1ba450208f3e2b71b3ee@167.235.117.93:29856,13e92186caf6414171a3660bc39dc6b800f03d5b@5.189.151.202:26676,3ed902bed4e1a54ae972f66469f5f7b9f1ba4da8@65.108.230.161:16656,b7e01ffbe25214f24bb42f0e805d02940a7224df@194.163.172.115:17656,866b505e44a2077def00ede26999551bedb5bddd@161.97.79.109:26656,b0b56d944cf1cc569a1e77e0923e075bad94d755@78.46.74.23:26656,2c2b83b7f1023c381afd3d67f326d70de098cb47@95.217.202.49:30656,3d76fbbfd1968f70c16a4b3eac598ebc59b75058@139.59.26.16:26656,c5ef62186e9aad1f83cab06f91533d1d5709bba7@65.109.117.212:13093,31e7e419e1dcdb154f0b49a807dbc5f86d9c0b98@65.108.46.100:29656"  
SNAP_RPC="https://rpc.okp4.aknodes.net:443"

sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.okp4d/config/config.toml 

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height);
BLOCK_HEIGHT=$((LATEST_HEIGHT - 1000));
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash) 

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH && sleep 2

sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ;
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ;
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ;
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ;
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.okp4d/config/config.toml

mv $HOME/.okp4d/priv_validator_state.json.backup $HOME/.okp4d/data/priv_validator_state.json

sudo systemctl restart okp4d && sudo journalctl -u okp4d -f
```
