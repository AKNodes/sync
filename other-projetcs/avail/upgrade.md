# Upgrade

version 2.2.0-fe7e86296d8

### Stop your node

```
sudo systemctl stop availd.service
```

<pre><code><strong>cd avail
</strong></code></pre>

```
sudo apt update
sudo apt upgrade
```

### Remove avail-node and download the latest binary

```
rm -rf avail-node
```

```
wget https://github.com/availproject/avail/releases/download/v2.2.5.0-rc1/x86_64-ubuntu-2404-avail-node.tar.gz
```

### Extract the file

```
tar -xf x86_64-ubuntu-2404-avail-node.tar.gz
```



### Restart the node & Check Service Logs

```
sudo systemctl restart availd.service & journalctl -u availd.service -f
```
