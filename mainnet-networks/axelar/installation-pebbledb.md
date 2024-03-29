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
cd $HOME
rm -rf axelar-core
git clone https://github.com/axelarnetwork/axelar-core.git
cd axelar-core
git checkout v0.34.1

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
axelard init "$MONIKER" --chain-id=axelar-dojo-1
```

### Node Config

```
# Download genesis and addrbook
curl -Ls https://snapshots.aknodes.net/snapshots/axelar/genesis.json > $HOME/.axelar/config/genesis.json
curl -Ls https://snapshots.aknodes.net/snapshots/axelar/addrbook.json > $HOME/.axelar/config/addrbook.json

# Add seeds
PEERS="0c9555d92aaa7cda65d3456ef1a6ac6f24e055d5@65.21.123.54:26676,69266a777c64b9533403add14fb724e2eebfd848@65.108.122.247:26656,609504b517f88f628e98d4a918ffc69e9654b451@65.108.192.147:26656,5b262497ee26cb079766e53f1a74c8e8a0f5cf7a@65.109.52.178:26656,3d67d0646cddcc203b41434aceea64ade22ba6fc@18.217.111.172:26656,6acf3b257c4d839f4f2eb077eeb9a758ba4865e7@176.103.222.161:26656,5622ce45be98fc4f54a295ded742d7c7d7551285@65.108.200.49:27856,051a6fe084df02c6a4c484139899808eea841181@13.59.129.55:26656,4f12f80da0eda26c77f2780f755cea498198f8cd@3.142.113.84:26656,3bc24a2f1da2a3b0395e3b9b82b1507ae0bbce32@157.90.91.20:13656,5581d7215b95264e600209bfed0fa28a305620dd@3.134.196.244:26656,54e0c474ba49b1e78b09c9eff1a39ca3214c65a8@185.163.64.143:26656,f7061dc29a0ac18567848c1654e01b6a7a263051@51.158.156.171:36656,25255eaac0a7b4df8caa546a48d320d9cacd1f19@3.133.230.24:26656,803d85675bc116eee836d90a916a0a5f3d0d45fc@18.139.161.51:26656,dab362b5642b752f503ad066cb30f936fc0d2310@81.196.253.241:15656,a7b306b6421a0b474a0413905ffcae780d398ecd@65.108.232.134:53656,1136202f40f158b6e11257af1a34ce5379179d05@3.141.87.0:26656,57dc509d932efd27f378079c7959bcddd1131e90@136.243.174.45:56656,590a6723091c9f7049227b043bcbe84bdbcf3b57@198.244.165.175:15656,11f64e33e76755673705fba1db25a18059a333ab@65.21.74.228:26656,e6aeadba513e216954f7330834d0af0047fccce2@3.23.243.230:26656,1bd159908c6385a5c9767711c389cbe5d2027b1d@89.149.218.182:26656"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.axelar/config/config.toml


# Set minimum gas price
sed -i -e "s|^minimum-gas-prices *=.*|minimum-gas-prices = \"0.007uaxl\"|" $HOME/.axelar/config/app.toml

# prunning
pruning="custom"
pruning_keep_recent="50000"
pruning_keep_every="0"
pruning_interval="19"

sed -i "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.axelar/config/app.toml
sed -i "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.axelar/config/app.toml
sed -i "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.axelar/config/app.toml
sed -i "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.axelar/config/app.toml

# set pebbledb
db_backend="pebbledb"
sed -i "s/^db_backend *=.*/db_backend = \"$db_backend\"/" $HOME/.axelar/config/config.toml
sed -i "s/^app-db-backend *=.*/app-db-backend = \"$db_backend\"/" $HOME/.axelar/config/app.toml
```

### **Create Service**

```
tee /etc/systemd/system/axelard.service > /dev/null << EOF
[Unit]
Description=Axelar mainnet (PebbleDB) node service
After=network.target
[Service]
Type=simple
User=$USER
ExecStart=$(which axelard) start --home $HOME/.axelar
Restart=on-failure
RestartSec=10
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF

sudo systemctl daemon-reload
sudo systemctl enable axelard
```

### **Download Snapshot**

```
axelard tendermint unsafe-reset-all --home $HOME/.axelar --keep-addr-book 
curl https://snapshots.aknodes.net/snapshots/axelar/snapshot-axelar.AKNodes.lz4 | lz4 -dc - | tar -xf - -C $HOME/.axelar
```

### Start the node

```
sudo systemctl restart axelard
journalctl -u axelard -f
```

[buy me a cup of coffe](https://www.paypal.com/paypalme/AbdelAkridi?country.x=NL\&locale.x=en\_US)
