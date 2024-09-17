---
description: >-
  Minimum Hardware Requirements 4x CPUs; the faster clock speed the better  8GB
  RAM  100GB of storage (SSD or NVME)
---

# Installation

## <mark style="color:blue;">Setup validator name</mark> <a href="#setup-validator-name" id="setup-validator-name"></a>

```
MONIKER="YOUR_MONIKER_GOES_HERE"
```

### Install dependencies <a href="#install-dependencies" id="install-dependencies"></a>

```
sudo apt update
sudo apt-get install git curl build-essential make jq gcc snapd chrony lz4 tmux unzip bc -y
```

### **INSTALL GO**

```
sudo rm -rf /usr/local/go
curl -Ls https://go.dev/dl/go1.22.1.linux-amd64.tar.gz | sudo tar -xzf - -C /usr/local
eval $(echo 'export PATH=$PATH:/usr/local/go/bin' | sudo tee /etc/profile.d/golang.sh)
eval $(echo 'export PATH=$PATH:$HOME/go/bin' | tee -a $HOME/.profile)
```

### Install Node

```
export LAVA_BINARY=lavad

cd || return
rm -rf lava
git clone https://github.com/lavanet/lava.git
cd lava || return
git checkout v3.1.0
make install
```

**Initialize Node**

```
lavad init "$MONIKER" --chain-id=lava-mainnet-1
```

### Download genesis and addrbook

<pre><code><strong>curl -Ls https://snapshots.aknodes.net/snapshots/lava-mainnet/genesis.json > $HOME/.lava/config/genesis.json
</strong></code></pre>

```
curl -Ls https://snapshots.aknodes.net/snapshots/lava-mainnet/addrbook.json > $HOME/.lava/config/addrbook.json
```

### **Create Service**

```
sudo tee /etc/systemd/system/lavad.service > /dev/null <<EOF
[Unit]
Description=lavad Daemon
After=network-online.target
[Service]
User=$USER
ExecStart=$(which lavad) start
Restart=always
RestartSec=3
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF
sudo systemctl daemon-reload
sudo systemctl enable lavad
```

### **Download Snapshot**

```
lavad tendermint unsafe-reset-all --home $HOME/.lava --keep-addr-book 
curl https://snapshots.aknodes.net/snapshots/lava-mainnet/snapshot-lava-mainnet.AKNodes.lz4 | lz4 -dc - | tar -xf - -C $HOME/.lava
```

### Start the node

```
sudo systemctl restart lavad
journalctl -u lavad -f
```
