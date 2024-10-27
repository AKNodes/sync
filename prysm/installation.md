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
rm -rf $HOME/go
sudo rm -rf /usr/local/go
cd $HOME
curl https://dl.google.com/go/go1.22.2.linux-amd64.tar.gz | sudo tar -C/usr/local -zxvf -
cat <<'EOF' >>$HOME/.profile
export GOROOT=/usr/local/go
export GOPATH=$HOME/go
export GO111MODULE=on
export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin
EOF
source $HOME/.profile
go version
```

### Install Node

```
cd $HOME
git clone https://github.com/kleomedes/prysm prysm
cd prysm
git checkout v0.1.0-devnet
make install
```



## **Initialize Node**

```
prysmd init $MONIKER --chain-id prysm-devnet-1
```

### Download genesis and addrbook

<pre><code><strong>curl -Ls https://snapshots.aknodes.net/snapshots/prysm/genesis.json > $HOME/.prysm/config/genesis.json
</strong></code></pre>

```
curl -Ls https://snapshots.aknodes.net/snapshots/prysm/addrbook.json > $HOME/.prysm/config/addrbook.json
```

### **Create Service**

```
tee /etc/systemd/system/prysmd.service > /dev/null <<EOF
[Unit]
Description=prysmd
After=network-online.target

[Service]
User=$USER
ExecStart=$(which prysmd) start
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```

### **Download Snapshot**

```
prysmd tendermint unsafe-reset-all --home $HOME/.prysm --keep-addr-book 
curl https://snapshots.aknodes.net/snapshots/symphony/snapshot-prysm.AKNodes.lz4 | lz4 -dc - | tar -xf - -C $HOME/.prysm
```

### Start the node

```
sudo systemctl restart prysmd
journalctl -u prysmd -f
```

