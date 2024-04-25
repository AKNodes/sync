# Sync

## Snapshot

Update every 6 hours

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

