---
hidden: true
---

# gitopia upgrade

#### **1️⃣ Backup & Prepare**

```bash
cd $HOME
# Optional backup of current node
mv gitopia gitopia_backup_$(date +%F_%T)
```

***

#### **2️⃣ Clone Gitopia v6.0.0**

```bash
git clone https://github.com/gitopia/gitopia.git
cd gitopia
git fetch --all
git checkout v6.0.0
```

***

#### **3️⃣ Build the binary**

```bash
mkdir -p build
go build -mod=readonly -tags "netgo ledger" \
-ldflags '-X github.com/cosmos/cosmos-sdk/version.Name=gitopia \
-X github.com/cosmos/cosmos-sdk/version.AppName=gitopiad \
-X github.com/cosmos/cosmos-sdk/version.Version=6.0.0 \
-X github.com/cosmos/cosmos-sdk/version.Commit=$(git rev-parse HEAD) \
-X "github.com/cosmos/cosmos-sdk/version.BuildTags=netgo ledger," \
-X github.com/cometbft/cometbft/version.TMCoreSemVer=v0.37.6 -w -s' \
-trimpath -o build/ ./...
```

Verify the binary exists:

```bash
ls build
# Should show: gitopiad
```

***

#### **4️⃣ Move binary to Cosmovisor upgrade folder**

```bash
mkdir -p $HOME/.gitopia/cosmovisor/upgrades/v6.0.0/bin
mv build/gitopiad $HOME/.gitopia/cosmovisor/upgrades/v6.0.0/bin/
```

Check version:

```bash
$HOME/.gitopia/cosmovisor/upgrades/v6.0.0/bin/gitopiad version
# Should output: 6.0.0
```

***

#### **5️⃣ Clean old upgrades (optional)**

```bash
cd $HOME/.gitopia/cosmovisor/upgrades
rm -rf v5.1.0
ls
# Should show: v4  v6.0.0
```

***

#### **6️⃣ Restart Gitopia service**

```bash
sudo systemctl restart gitopia.service
sudo journalctl -u gitopia.service -f
```

* Cosmovisor will automatically pick the v6.0.0 binary.
* Logs should confirm the node is running the new version.

***

#### **7️⃣ Confirm**

```bash
ps aux | grep gitopiad
```
