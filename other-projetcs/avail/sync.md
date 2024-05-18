# Sync

**Updated every 6 hours  ( 620 MB )**

Stop the node and remove the old data file ( paritydb )

```
sudo systemctl stop availd.service
```

```
cd avail/node-data/chains/avail_turing_network
```

```
rm -rf paritydb
```

Download the snapshot&#x20;

```
wget https://snapshots.aknodes.net/snapshots/avail/snapshot-avail-turing.lz4
```

Exract the file

```
lz4 -c -d snapshot-avail-turing.lz4  | tar -x -C /root/avail/node-data/chains/avail_turing_network
```

Restart the node and see the logs

```
sudo systemctl restart availd.service & journalctl -u availd.service -u
```
