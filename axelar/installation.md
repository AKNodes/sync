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

rm -rf axelar-core

git clone https://github.com/axelarnetwork/axelar-core.git

cd axelar-core

git checkout v0.34.1

make build
```

## **Initialize Node**

```
axelard init "$MONIKER" --chain-id=axelar-testnet-lisbon-3
```

### Download genesis and addrbook

<pre><code><strong>curl -Ls https://snapshots.aknodes.net/snapshots/axelar-testnet/genesis.json > $HOME/.axelar/config/genesis.json
</strong></code></pre>

```
curl -Ls https://snapshots.aknodes.net/snapshots/axelar-testnet/addrbook.json > $HOME/.axelar/config/addrbook.json
```

### **Create Service**

```
sudo tee /etc/systemd/system/axelard.service > /dev/null <<EOF
[Unit]
Description=axelard Daemon
After=network-online.target
[Service]
User=$USER
ExecStart=$(which axelard) start
Restart=always
RestartSec=3
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF
sudo systemctl daemon-reload
sudo systemctl enable axelard
```

### **Download Snapshot**

```
axelard tendermint unsafe-reset-all --home $HOME/.axelar --keep-addr-book 
curl https://snapshots.aknodes.net/snapshots/axelar-testnet/snapshot-axelar-testnet.AKNodes.lz4 | lz4 -dc - | tar -xf - -C $HOME/.axelar
```

### Start the node

```
sudo systemctl restart axelard
journalctl -u axelard -f
```

[buy me a cup of coffe](https://www.paypal.com/paypalme/AbdelAkridi?country.x=NL\&locale.x=en\_US)
