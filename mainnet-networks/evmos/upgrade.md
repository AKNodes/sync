# Upgrade

#### 1. Download and build old binaries

```bash
sudo systemctl stop evmosd

cd $HOME
rm -rf evmos
git clone https://github.com/evmos/evmos.git
cd evmos
git checkout v15.0.0

go mod edit -replace github.com/tendermint/tm-db=github.com/notional-labs/tm-db@v0.6.8-pebble
go mod tidy
go mod edit -replace github.com/cometbft/cometbft-db=github.com/notional-labs/cometbft-db@pebble
go mod tidy

go install -ldflags '-w -s -X github.com/cosmos/cosmos-sdk/types.DBBackend=pebbledb -X github.com/tendermint/tm-db.ForceSync=1 ' -tags pebbledb ./...

sudo systemctl start evmosd && journalctl -u evmosd -f -o cat
```

#### 2. After panic Download and build upgrade binaries

```bash
sudo systemctl stop evmosd
cd $HOME/evmos
git reset --hard
git pull
git checkout v16.0.0

go mod edit -replace github.com/tendermint/tm-db=github.com/notional-labs/tm-db@v0.6.8-pebble
go mod tidy
go mod edit -replace github.com/cometbft/cometbft-db=github.com/notional-labs/cometbft-db@pebble
go mod tidy

go install -ldflags "-w -s -X github.com/cosmos/cosmos-sdk/types.DBBackend=pebbledb \
 -X github.com/cosmos/cosmos-sdk/version.Version=$(git describe --tags)-pebbledb \
 -X github.com/cosmos/cosmos-sdk/version.Commit=$(git log -1 --format='%H')" -tags pebbledb ./...

sudo systemctl start evmosd && journalctl -u evmosd -f -o cat
```
