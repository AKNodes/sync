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
cd $HOME && \
wget -O pellcored https://github.com/0xPellNetwork/network-config/releases/download/v1.2.1/pellcored-v1.2.1-linux-amd64 && \
chmod +x pellcored && \
mv pellcored ~/go/bin/ && \
WASMVM_VERSION=v2.1.2 && \
export LD_LIBRARY_PATH=~/.pellcored/lib && \
mkdir -p $LD_LIBRARY_PATH && \
wget "https://github.com/CosmWasm/wasmvm/releases/download/$WASMVM_VERSION/libwasmvm.$(uname -m).so" -O "$LD_LIBRARY_PATH/libwasmvm.$(uname -m).so" && \
echo "export LD_LIBRARY_PATH=$HOME/.pellcored/lib:$LD_LIBRARY_PATH" >> $HOME/.bash_profile && \
source ~/.bash_profile

```

## **Initialize Node**

```
pellcored init "$MONIKER" --chain-id=ignite_186-1
```

### Download genesis and addrbook

<pre><code><strong>curl -Ls https://snapshots.aknodes.net/snapshots/pellcore/genesis.json > $HOME/.pellcored/config/genesis.json
</strong></code></pre>

```
curl -Ls https://snapshots.aknodes.net/snapshots/pellcore/addrbook.json > $HOME/.pellcored/config/addrbook.json
```

### **Create Service**

```
sudo tee /etc/systemd/system/pellcored.service > /dev/null <<EOF
[Unit]
Description=pellcored Daemon
After=network-online.target
[Service]
User=$USER
ExecStart=$(which pellcored) start
Restart=always
RestartSec=3
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF
sudo systemctl daemon-reload
sudo systemctl enable pellcored
```

### **Download Snapshot**

```
pellcored tendermint unsafe-reset-all --home $HOME/.arkeo --keep-addr-book 
curl https://snapshots.aknodes.net/snapshots/pellcore/snapshot-pellcore.AKNodes.lz4 | lz4 -dc - | tar -xf - -C $HOME/.pellcored
```

### Start the node

```
sudo systemctl restart pellcored
journalctl -u pellcored -f
```
