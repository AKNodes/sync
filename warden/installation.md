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
cd $HOME && mkdir -p go/bin/
git clone --depth 1 --branch v0.1.0 https://github.com/warden-protocol/wardenprotocol/
cd  wardenprotocol/warden/cmd/wardend
go build
sudo mv wardend $HOME/go/bin/
```

## **Initialize Node**

```
wardend init MyNode --chain-id alfama
```

### Download genesis and addrbook

<pre><code><strong>curl -Ls https://snapshots.aknodes.net/snapshots/warden/genesis.json > $HOME/.warden/config/genesis.json
</strong></code></pre>

```
curl -Ls https://snapshots.aknodes.net/snapshots/warden/addrbook.json > $HOME/.warden/config/addrbook.json
```

### **Create Service**

```
tee /etc/systemd/system/wardend.service > /dev/null <<EOF
[Unit]
Description=wardend
After=network-online.target

[Service]
User=$USER
ExecStart=$(which wardend) start
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```

### **Download Snapshot**

```
wardend tendermint unsafe-reset-all --home $HOME/.warden --keep-addr-book 
curl https://snapshots.aknodes.net/snapshots/warden/snapshot-warden.AKNodes.lz4 | lz4 -dc - | tar -xf - -C $HOME/.warden
```

### Start the node

```
sudo systemctl restart wardend
journalctl -u wardend -f
```
