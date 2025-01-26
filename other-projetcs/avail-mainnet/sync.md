# Sync

**Updated every 12 hours  ( 16.9GB )**

Stop the node and remove the old data file make sure to find the location of the file before using the command ( paritydb )

```
sudo systemctl stop availd.service
```

```
cd avail/node-data/chains/avail_da_mainnet
```

```
rm -rf paritydb
```

Download the snapshot&#x20;

```
wget https://snapshots.aknodes.net/snapshots/avail-mainnet/snapshot-avail-mainnet.lz4
```

Exract the file

```
lz4 -c -d snapshot-avail-mainnet.lz4  | tar -x -C /root/avail/node-data/chains/avail_da_mainnet
```

Restart the node and see the logs

```
sudo systemctl restart availd.service & journalctl -u availd.service -f
```

