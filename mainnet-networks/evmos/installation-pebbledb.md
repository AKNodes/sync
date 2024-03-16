---
description: >-
  Minimum Hardware Requirements 4x CPUs; the faster clock speed the better  8GB
  RAM  100GB of storage (SSD or NVME)
---

# Installation (PebbleDB)

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
# Clone project repository
cd $HOME
rm -rf evmos
git clone https://github.com/evmos/evmos.git
cd evmos
git checkout v16.0.0

go mod edit -replace github.com/tendermint/tm-db=github.com/notional-labs/tm-db@v0.6.8-pebble
go mod tidy
go mod edit -replace github.com/cometbft/cometbft-db=github.com/notional-labs/cometbft-db@pebble
go mod tidy

go install -ldflags "-w -s -X github.com/cosmos/cosmos-sdk/types.DBBackend=pebbledb \
 -X github.com/cosmos/cosmos-sdk/version.Version=$(git describe --tags)-pebbledb \
 -X github.com/cosmos/cosmos-sdk/version.Commit=$(git log -1 --format='%H')" -tags pebbledb ./...
```

## **Initialize Node**

```
evmosd config chain-id evmos_9001-2
evmosd config keyring-backend file
evmosd config node tcp://localhost:26657


evmosd init $MONIKER --chain-id evmos_9001-2
```

### Node Config

```
# Download genesis and addrbook
curl -Ls https://snapshots.aknodes.net/snapshots/evmos/genesis.json > $HOME/.evmosd/config/genesis.json
curl -Ls https://snapshots.aknodes.net/snapshots/evmos/addrbook.json > $HOME/.evmosd/config/addrbook.json

# Add seeds
sed -i 's/seeds = ""/seeds = "a56f27699b7e47ce79335509c0863bcfe6ae1347@rpc.evmos.nodestake.top:666"/' ~/.evmosd/config/config.toml

# Set minimum gas price
sed -i -e "s|^minimum-gas-prices *=.*|minimum-gas-prices = \"25000000aevmos\"|" $HOME/.evmosd/config/app.toml

# prunning
pruning="custom"
pruning_keep_recent="50000"
pruning_keep_every="0"
pruning_interval="19"

sed -i "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.evmosd/config/app.toml
sed -i "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.evmosd/config/app.toml
sed -i "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.evmosd/config/app.toml
sed -i "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.evmosd/config/app.toml

# snapshots
sed -i 's/snapshot-interval *=.*/snapshot-interval = 0/' $HOME/.evmosd/config/app.toml

# set pebbledb
db_backend="pebbledb"
sed -i "s/^db_backend *=.*/db_backend = \"$db_backend\"/" $HOME/.evmosd/config/config.toml
sed -i "s/^app-db-backend *=.*/app-db-backend = \"$db_backend\"/" $HOME/.evmosd/config/app.toml
```

### **Create Service**

```
sudo tee /etc/systemd/system/evmosd.service > /dev/null <<EOF
[Unit]
Description=evmosd Daemon
After=network-online.target
[Service]
User=$USER
ExecStart=$(which evmosd) start
Restart=always
RestartSec=3
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF
sudo systemctl daemon-reload
sudo systemctl enable evmosd
```

### **Download Snapshot**

```
evmosd tendermint unsafe-reset-all --home $HOME/.evmosd --keep-addr-book 
curl https://snapshots.aknodes.net/snapshots/evmos/snapshot-evmos.AKNodes.lz4 | lz4 -dc - | tar -xf - -C $HOME/.evmosd
```

### Start the node

```
sudo systemctl restart evmosd
journalctl -u evmosd -f
```

[buy me a cup of coffe](https://www.paypal.com/paypalme/AbdelAkridi?country.x=NL\&locale.x=en\_US)
