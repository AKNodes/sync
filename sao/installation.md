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

rm -rf sao-consensus

git clone https://github.com/SaoNetwork/sao-consensus.git

cd sao-consensus

git checkout v0.1.9

make install

```

## **Initialize Node**

```
saod init "$MONIKER" --chain-id=sao-20230629
```

### Download genesis and addrbook

<pre><code><strong>curl -Ls https://snapshots.aknodes.net/snapshots/sao/genesis.json > $HOME/.sao/config/genesis.json
</strong></code></pre>

```
curl -Ls https://snapshots.aknodes.net/snapshots/sao/addrbook.json > $HOME/.sao/config/addrbook.json
```

### **Create Service**

```
sudo tee /etc/systemd/system/saod.service > /dev/null <<EOF
[Unit]
Description=saod Daemon
After=network-online.target
[Service]
User=$USER
ExecStart=$(which saod) start
Restart=always
RestartSec=3
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF
sudo systemctl daemon-reload
sudo systemctl enable saod
```

### **Download Snapshot**

```
saod tendermint unsafe-reset-all --home $HOME/.sao --keep-addr-book 
curl https://snapshots.aknodes.net/snapshots/sao/snapshot-sao.AKNodes.lz4 | lz4 -dc - | tar -xf - -C $HOME/.sao
```

### Start the node

```
sudo systemctl restart saod
journalctl -u saod -f
```

[buy me a cup of coffe](https://www.paypal.com/paypalme/AbdelAkridi?country.x=NL\&locale.x=en\_US)
