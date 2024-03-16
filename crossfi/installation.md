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
mkdir -p $HOME/crossfi && cd $HOME/crossfi
#mkdir -p ~/go/bin

wget https://github.com/crossfichain/crossfi-node/releases/download/v0.3.0-prebuild3/crossfi-node_0.3.0-prebuild3_linux_amd64.tar.gz
tar -xvf crossfi-node_0.3.0-prebuild3_linux_amd64.tar.gz
chmod +x $HOME/crossfi/bin/crossfid
mv $HOME/crossfi/bin/crossfid $HOME/go/bin
rm -rf crossfi-node_0.3.0-prebuild3_linux_amd64.tar.gz
```



## **Initialize Node**

```
crossfid config chain-id crossfi-evm-testnet-1
```

### Download genesis and addrbook

<pre><code><strong>curl -Ls https://snapshots.aknodes.net/snapshots/crossfi/genesis.json > $HOME/.mineplex-chain/config/genesis.json
</strong></code></pre>

```
curl -Ls https://snapshots.aknodes.net/snapshots/crossfi/addrbook.json > $HOME/.mineplex-chain/config/addrbook.json
```

### **Create Service**

```
tee /etc/systemd/system/crossfid.service > /dev/null <<EOF
[Unit]
Description=crossfid
After=network-online.target

[Service]
User=$USER
ExecStart=$(which crossfid) start
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```

### **Download Snapshot**

```
crossfid tendermint unsafe-reset-all --home $HOME/.mineplex-chain --keep-addr-book 
curl https://snapshots.aknodes.net/snapshots/crossfi/snapshot-crossfi.AKNodes.lz4 | lz4 -dc - | tar -xf - -C $HOME/.mineplex-chain
```

### Start the node

```
sudo systemctl restart crossfid
journalctl -u crossfid -f
```
