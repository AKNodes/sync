# Sync

**Updated every 12 hours  ( 1.7GB )**

Stop the node and remove the old data file make sure to find the location of the file before using the command ( paritydb )

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
