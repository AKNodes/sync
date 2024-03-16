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
rm -rf artela
git clone https://github.com/artela-network/artela
cd artela
git checkout v0.4.7-rc4
make install

```

## **Initialize Node**

```
artelad init "test" --chain-id artela_11822-1
```

### Download genesis and addrbook

<pre><code><strong>curl -Ls https://snapshots.aknodes.net/snapshots/artela/genesis.json > $HOME/.artelad/config/genesis.json
</strong></code></pre>

```
curl -Ls https://snapshots.aknodes.net/snapshots/artela/addrbook.json > $HOME/.artelad/config/addrbook.json
```

### **Create Service**

```
sudo tee /etc/systemd/system/artelad.service > /dev/null <<EOF
[Unit]
Description=Artela node
After=network-online.target
[Service]
User=$USER
WorkingDirectory=$HOME/.artelad
ExecStart=$(which artelad) start --home $HOME/.artelad
Restart=on-failure
RestartSec=5
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF
```

### **Download Snapshot**

```
artelad tendermint unsafe-reset-all --home $HOME/.dora --keep-addr-book 
curl https://snapshots.aknodes.net/snapshots/artela/snapshot-artela.AKNodes.lz4 | lz4 -dc - | tar -xf - -C $HOME/.artelad
```

### Start the node

```
sudo systemctl restart artelad
journalctl -u artelad -f
```
