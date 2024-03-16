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
curl https://dl.google.com/go/go1.20.5.linux-amd64.tar.gz | sudo tar -C/usr/local -zxvf -
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

rm -rf entangle-blockchain

git clone https://github.com/Entangle-Protocol/entangle-blockchain

cd entangle-blockchain

make install

```

## **Initialize Node**

```
entangled init "$MONIKER" --chain-id=entangle_33133-1
```

### Download genesis and addrbook

<pre><code><strong>curl -Ls https://snapshots.aknodes.net/snapshots/entangle/genesis.json > $HOME/.entangled/config/genesis.json
</strong></code></pre>

```
curl -Ls https://snapshots.aknodes.net/snapshots/entangle/addrbook.json > $HOME/.entangled/config/addrbook.json
```

### **Create Service**

```
sudo tee /etc/systemd/system/entangled.service > /dev/null <<EOF
[Unit]
Description=entangled Daemon
After=network-online.target
[Service]
User=$USER
ExecStart=$(which entangled) start
Restart=always
RestartSec=3
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF
sudo systemctl daemon-reload
sudo systemctl enable entangled
```

### **Download Snapshot**

```
entangled tendermint unsafe-reset-all --home $HOME/.entangled --keep-addr-book 
curl https://snapshots.aknodes.net/snapshots/entangle/snapshot-entangle.AKNodes.lz4 | lz4 -dc - | tar -xf - -C $HOME/.entangled
```

### Start the node

```
sudo systemctl restart entangled
journalctl -u entangled -f
```

[buy me a cup of coffe](https://www.paypal.com/paypalme/AbdelAkridi?country.x=NL\&locale.x=en\_US)
