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
cd || return

rm -rf okp4d

git clone https://github.com/okp4/okp4d.git

cd okp4d || return

git checkout v5.0.0

make install

```

## **Initialize Node**

```
okp4d init "$MONIKER" --chain-id=okp4-nemeton-1
```

### Download genesis and addrbook

<pre><code><strong>curl -Ls https://snapshots.aknodes.net/snapshots/okp4/genesis.json > $HOME/.okp4d/config/genesis.json
</strong></code></pre>

```
curl -Ls https://snapshots.aknodes.net/snapshots/okp4/addrbook.json > $HOME/.okp4d/config/addrbook.json
```

### **Create Service**

```
sudo tee /etc/systemd/system/okp4d.service > /dev/null <<EOF
[Unit]
Description=okp4d Daemon
After=network-online.target
[Service]
User=$USER
ExecStart=$(which okp4d) start
Restart=always
RestartSec=3
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF
sudo systemctl daemon-reload
sudo systemctl enable okp4d
```

### **Download Snapshot**

```
okp4d tendermint unsafe-reset-all --home $HOME/.okp4d --keep-addr-book 
curl https://snapshots.aknodes.net/snapshots/okp4/snapshot-okp4.AKNodes.lz4 | lz4 -dc - | tar -xf - -C $HOME/.okp4d
```

### Start the node

```
sudo systemctl restart okp4d
journalctl -u okp4d -f
```

[buy me a cup of coffe](https://www.paypal.com/paypalme/AbdelAkridi?country.x=NL\&locale.x=en\_US)
