# Installation

### Setup validator name <a href="#setup-validator-name" id="setup-validator-name"></a>

```
MONIKER="YOUR_MONIKER_GOES_HERE"
```

#### Install dependencies <a href="#install-dependencies" id="install-dependencies"></a>

```
sudo apt update
sudo apt-get install git curl build-essential make jq gcc snapd chrony lz4 tmux unzip bc -y
```

#### **INSTALL GO** <a href="#install-go" id="install-go"></a>

```
rm -rf $HOME/go
sudo rm -rf /usr/local/go
cd $HOME
curl https://dl.google.com/go/go1.19.13.linux-amd64.tar.gz | sudo tar -C/usr/local -zxvf -
cat <<'EOF' >>$HOME/.profile
export GOROOT=/usr/local/go
export GOPATH=$HOME/go
export GO111MODULE=on
export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin
EOF
source $HOME/.profile
go version
```

#### Install Node <a href="#install-node" id="install-node"></a>

```
cd $HOME
mkdir -p $HOME/go/bin/
cd $HOME
rm -rf gitopia
git clone https://github.com/gitopia/gitopia.git
cd gitopia
git checkout v4.0.0

make build
```

### **Initialize Node** <a href="#initialize-node" id="initialize-node"></a>

```
gitopiad init MyNode --chain-id gitopia
```

#### &#x20;<a href="#download-genesis-and-addrbook" id="download-genesis-and-addrbook"></a>

#### Download genesis and addrbook <a href="#download-genesis-and-addrbook" id="download-genesis-and-addrbook"></a>

```
curl -Ls https://snapshots.aknodes.net/snapshots/gitopia/genesis.json > $HOME/.gitopia/config/genesis.json
```

```
curl -Ls https://snapshots.aknodes.net/snapshots/gitopia/addrbook.json > $HOME/.gitopia/config/addrbook.json
```

#### &#x20;<a href="#create-service" id="create-service"></a>

#### **Create Service** <a href="#create-service" id="create-service"></a>

```
tee /etc/systemd/system/gitopiad.service > /dev/null <<EOF
[Unit]
Description=gitopiad
After=network-online.target

[Service]
User=$USER
ExecStart=$(which gitopiad) start
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```

#### &#x20;<a href="#download-snapshot" id="download-snapshot"></a>

#### **Download Snapshot** <a href="#download-snapshot" id="download-snapshot"></a>

```
hedged tendermint unsafe-reset-all --home $HOME/.gitopia --keep-addr-book 
curl https://snapshots.aknodes.net/snapshots/gitopia/snapshot-gitopia.AKNodes.lz4 | lz4 -dc - | tar -xf - -C $HOME/.gitopia
```

#### &#x20;<a href="#start-the-node" id="start-the-node"></a>

#### Start the node <a href="#start-the-node" id="start-the-node"></a>

```
sudo systemctl restart gitopiad
journalctl -u gitopiad -f
```
