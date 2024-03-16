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
<strong>rm -rf doravota
</strong>git clone https://github.com/DoraFactory/doravota.git
cd doravota
git checkout 0.2.0
make install
dorad version

</code></pre>

## **Initialize Node**

```
dorad init NodeName --chain-id=vota-vk
```

### Download genesis and addrbook

<pre><code><strong>curl -Ls https://snapshots.aknodes.net/snapshots/dora/genesis.json > $HOME/.dora/config/genesis.json
</strong></code></pre>

```
curl -Ls https://snapshots.aknodes.net/snapshots/dora/addrbook.json > $HOME/.dora/config/addrbook.json
```

### **Create Service**

```
sudo tee /etc/systemd/system/dorad.service > /dev/null <<EOF
[Unit]
Description=dorad Daemon
After=network-online.target
[Service]
User=$USER
ExecStart=$(which dorad) start
Restart=always
RestartSec=3
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF
sudo systemctl daemon-reload
sudo systemctl enable dorad
```

### **Download Snapshot**

```
doead tendermint unsafe-reset-all --home $HOME/.dora --keep-addr-book 
curl https://snapshots.aknodes.net/snapshots/dora/snapshot-dora.AKNodes.lz4 | lz4 -dc - | tar -xf - -C $HOME/.dora
```

### Start the node

```
sudo systemctl restart dorad
journalctl -u dorad -f
```
