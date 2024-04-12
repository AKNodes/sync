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

mkdir -p /root/go/bin/

wget https://snapshots.aknodes.net/snapshots/arkeo/arkeod

chmod +x arkeod

mv arkeod /root/go/bin/

arkeod version
```

## **Initialize Node**

```
arkeod init "$MONIKER" --chain-id=arkeo
```

### Download genesis and addrbook

<pre><code><strong>curl -Ls https://snapshots.aknodes.net/snapshots/arkeo/genesis.json > $HOME/.arkeo/config/genesis.json
</strong></code></pre>

```
curl -Ls https://snapshots.aknodes.net/snapshots/arkeo/addrbook.json > $HOME/.arkeo/config/addrbook.json
```

### **Create Service**

```
sudo tee /etc/systemd/system/arkeod.service > /dev/null <<EOF
[Unit]
Description=arkeod Daemon
After=network-online.target
[Service]
User=$USER
ExecStart=$(which arkeod) start
Restart=always
RestartSec=3
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF
sudo systemctl daemon-reload
sudo systemctl enable arkeod
```

### **Download Snapshot**

```
arkeod tendermint unsafe-reset-all --home $HOME/.arkeo --keep-addr-book 
curl https://snapshots.aknodes.net/snapshots/arkeo/snapshot-arkeo.AKNodes.lz4 | lz4 -dc - | tar -xf - -C $HOME/.arkeo
```

### Start the node

```
sudo systemctl restart arkeod
journalctl -u arkeod -f
```

[buy me a cup of coffe](https://www.paypal.com/paypalme/AbdelAkridi?country.x=NL\&locale.x=en\_US)
