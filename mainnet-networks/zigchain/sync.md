# Sync

```
sudo systemctl stop zigchaind

cp $HOME/.zigchain/data/priv_validator_state.json $HOME/.zigchain/priv_validator_state.json.backup

zigchaind tendermint unsafe-reset-all --home $HOME/.zigchain --keep-addr-book

curl https://snapshots.aknodes.net/snapshots/zigchain-mainnet/snapshot-zigchain-mainnet.AKNodes.lz4 \
| lz4 -dc - | tar -xf - -C $HOME/.zigchain

mv $HOME/.zigchain/priv_validator_state.json.backup $HOME/.zigchain/data/priv_validator_state.json

sudo systemctl restart zigchaind
sudo journalctl -u zigchaind -f --no-hostname -o cat
```
