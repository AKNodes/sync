# Sync

## Snapshot

Update every 6 hours

```
sudo systemctl stop mantrachaind

cp $HOME/.mantrachain/data/priv_validator_state.json $HOME/.mantrachain/priv_validator_state.json.backup 

mantrachaind tendermint unsafe-reset-all --home $HOME/.mantrachain --keep-addr-book 
curl https://snapshots.aknodes.net/snapshots/mantrachain-mainnet/snapshot-mantrachain-mainnet.AKNodes.lz4 | lz4 -dc - | tar -xf - -C $HOME/.mantrachain

mv $HOME/.mantrachain/priv_validator_state.json.backup $HOME/.mantrachain/data/priv_validator_state.json 

sudo systemctl restart mantrachaind
sudo journalctl -u mantrachaind -f --no-hostname -o cat
```
