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

rm -rf haqq

git clone https://github.com/haqq-network/haqq.git

cd haqq

git checkout v1.6.2

make install
```

## **Initialize Node**

```
haqqd init "$MONIKER" --chain-id=haqq_11235-1
```

### Download genesis and addrbook

<pre><code><strong>curl -Ls https://snapshots.aknodes.net/snapshots/haqq/genesis.json > $HOME/.haqqd/config/genesis.json
</strong></code></pre>

```
curl -Ls https://snapshots.aknodes.net/snapshots/haqq/addrbook.json > $HOME/.haqqd/config/addrbook.json
```

### **Create Service**

```
sudo tee /etc/systemd/system/haqqd.service > /dev/null <<EOF
[Unit]
Description=haqqd Daemon
After=network-online.target
[Service]
User=$USER
ExecStart=$(which haqqd) start
Restart=always
RestartSec=3
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF
sudo systemctl daemon-reload
sudo systemctl enable haqqd
```

### **Download Snapshot**

```
axelard tendermint unsafe-reset-all --home $HOME/.haqq --keep-addr-book 
curl https://snapshots.aknodes.net/snapshots/haqq/snapshot-haqq.AKNodes.lz4 | lz4 -dc - | tar -xf - -C $HOME/.haqqd
```

### Start the node

```
sudo systemctl restart haqqd
```

[buy me a cup of coffe](https://www.paypal.com/paypalme/AbdelAkridi?country.x=NL\&locale.x=en\_US)
