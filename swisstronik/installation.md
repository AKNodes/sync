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
rm -rf swisstronik
git clone  swisstronik
cd swisstronik
git checkout v1.0.1
make i<a data-footnote-ref href="#user-content-fn-1">ns</a>tall
</code></pre>

## Official docs : [https://swisstronik.gitbook.io/swisstronik-docs/node-setup/setup-node/](https://swisstronik.gitbook.io/swisstronik-docs/node-setup/setup-node/install-from-.deb-package)

## **Initialize Node**

```
swisstronikd init MyNode --chain-in swisstronik_1291-1
```

### Download genesis and addrbook

<pre><code><strong>curl -Ls https://snapshots.aknodes.net/snapshots/swisstronik/genesis.json > $HOME/.swisstronik/config/genesis.json
</strong></code></pre>

```
curl -Ls https://snapshots.aknodes.net/snapshots/swisstronik/addrbook.json > $HOME/.swisstronik/config/addrbook.json
```

### **Create Service**

```
sudo tee /etc/systemd/system/swisstronikd.service > /dev/null <<EOF
[Unit]
Description=swisstronik node
After=network-online.target
[Service]
User=$USER
WorkingDirectory=$HOME/.swisstronik
ExecStart=$(which swisstronikd) start --home $HOME/.swisstronik
Restart=on-failure
RestartSec=5
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF
```

### **Download Snapshot**

```
swisstronikd tendermint unsafe-reset-all --home $HOME/.swisstronik --keep-addr-book 
curl https://snapshots.aknodes.net/snapshots/swisstronik/snapshot-swisstronik.AKNodes.lz4 | lz4 -dc - | tar -xf - -C $HOME/.swisstronik
```

### Start the node

```
sudo systemctl restart swisstronikd
journalctl -u swisstronikd -f
```

[^1]: 
