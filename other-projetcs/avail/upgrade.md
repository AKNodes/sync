# Upgrade



### Stop your node

```
sudo systemctl stop availd.service
```

```
cd avail
```

### Remove avail-node and download the latest binary

```
rm -rf avail-node
```

```
wget https://github.com/availproject/avail/releases/download/v2.2.3.0-rc1/x86_64-ubuntu-2204-avail-node.tar.gz
```

### Extract the file

```
tar -xf x86_64-ubuntu-2204-avail-node.tar.gz
```



### Restart the node & Check Service Logs

```
sudo systemctl restart availd.service & journalctl -u availd.service -f
```
