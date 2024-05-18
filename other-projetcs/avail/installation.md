---
description: >-
  Minimum Hardware Requirements 4x CPUs; the faster clock speed the better  8GB
  RAM  100GB of storage (SSD or NVME)
---

# Installation



### Install dependencies <a href="#install-dependencies" id="install-dependencies"></a>

```
sudo apt update
sudo apt-get install git curl build-essential make jq gcc snapd chrony lz4 tmux unzip bc -y
```

### **Install Rust**&#x20;

```
curl https://sh.rustup.rs -sSf | sh
```

### Create avail file

```
mkdir avail
```

### Enter the file

```
cd avail
```

### **Download the binary**

```
wget https://github.com/availproject/avail/releases/download/v2.2.2.0-rc1/x86_64-ubuntu-2204-avail-node.tar.gz
```

### Extract the file

```
tar -xf x86_64-ubuntu-2204-avail-node.tar.gz
```

### Create the service file

```
sudo tee /etc/systemd/system/availd.service > /dev/null <<EOF
[Unit]
Description=Avail Validator
After=network.target
StartLimitIntervalSec=0
[Service]
User=root
ExecStart= /root/avail/avail-node -d /root/avail/node-data --chain turing --validator --name "YOURVALIDATORNAME"
Restart=always
RestartSec=120
[Install]
WantedBy=multi-user.target
EOF
```

### Enable and start the service

```
sudo systemctl daemon-reload
sudo systemctl enable availd.service
sudo systemctl restart availd && sudo journalctl -u availd -f
```
