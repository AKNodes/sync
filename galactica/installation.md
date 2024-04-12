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

<pre><code>cd $HOME
<strong>git clone https://github.com/Galactica-corp/galactica
</strong>cd galactica
git checkout v0.1.2
make install
</code></pre>



## **Initialize Node**

```
galacticad config chain-id galactica_9302-1
```

### Download genesis and addrbook

<pre><code><strong>curl -Ls https://snapshots.aknodes.net/snapshots/galactica/genesis.json > $HOME/.galactica/config/genesis.json
</strong></code></pre>

```
curl -Ls https://snapshots.aknodes.net/snapshots/galactica/addrbook.json > $HOME/.galactica/config/addrbook.json
```

### **Create Service**

```
tee /etc/systemd/system/galacticad.service > /dev/null <<EOF
[Unit]
Description=galacticad
After=network-online.target

[Service]
User=$USER
ExecStart=$(which galacticad) start
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```

### **Download Snapshot**

```
galacticad tendermint unsafe-reset-all --home $HOME/.galactica --keep-addr-book 
curl https://snapshots.aknodes.net/snapshots/galactica/snapshot-galactica.AKNodes.lz4 | lz4 -dc - | tar -xf - -C $HOME/.galactica
```

### Start the node

```
sudo systemctl restart galacticad
journalctl -u galacticad -f
```
