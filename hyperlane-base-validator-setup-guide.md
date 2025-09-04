---
hidden: true
---

# Hyperlane Base Validator Setup Guide

##

***

### **1️⃣ System Update & Install Dependencies**

```bash
sudo apt-get update && sudo apt-get upgrade -y

# Install Docker
sudo apt-get install docker.io -y
```

***

### **2️⃣ Install Node.js (v20) via NVM**

```bash
# Install NVM
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.0/install.sh | bash

# Load NVM
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"

# Install Node.js v20
nvm install 20
```

***

### **3️⃣ Create a Wallet (using Foundry)**

```bash
# Install Foundry
curl -L https://foundry.paradigm.xyz | bash
source /root/.bashrc
foundryup

# Create a new wallet
cast wallet new
```

> Save your wallet address and private key securely. You will need ETH to cover validator announcement gas fees (\~0.00003 ETH).

***

### **4️⃣ Install Hyperlane CLI**

```bash
npm install -g @hyperlane-xyz/cli
```

***

### **5️⃣ Pull Hyperlane Docker Image**

```bash
docker pull --platform linux/amd64 gcr.io/abacus-labs-dev/hyperlane-agent:agents-v1.0.0
```

***

### **6️⃣ Create Database Directory**

```bash
mkdir -p /root/hyperlane_db_base
chmod -R 777 /root/hyperlane_db_base
```

***

### **7️⃣ Create Hyperlane Config JSON**

```bash
mkdir -p /root/hyperlane/config
```

Create `/root/hyperlane/config/Base.json` with this content:

```json
{
  "name": "Base",
  "chainId": 8453,
  "rpc": ["https://mainnet.base.org"],
  "explorer": "https://base.blockscout.com/",
  "proxyAdmin": "0x2f2aFaE1139Ce54feFC03593FeE8AB2aDF4a85A7",
  "mailbox": "0x0000000000000000000000000000000000000000",
  "confirmations": 1
}
```

Verify it:

```bash
cat /root/hyperlane/config/Base.json | jq .
```

***

### **8️⃣ Run Hyperlane Validator**

Replace the `PRIVATE_KEY` with your wallet private key and `NAME` with your chosen validator name (`AKNodes` in your case):

```bash
docker rm -f hyperlane-agent-base  # remove previous container if exists

docker run -d \
  -it \
  --name hyperlane-agent-base \
  --mount type=bind,source=/root/hyperlane_db_base,target=/hyperlane_db_base \
  --mount type=bind,source=/root/hyperlane/config,target=/usr/src/hyperlane/config \
  gcr.io/abacus-labs-dev/hyperlane-agent:agents-v1.0.0 \
  ./validator \
  --db /hyperlane_db_base \
  --originChainName Base \
  --reorgPeriod 1 \
  --validator.id AKNodes \
  --checkpointSyncer.type localStorage \
  --checkpointSyncer.folder Base \
  --checkpointSyncer.path /hyperlane_db_base/Base_checkpoints \
  --validator.key 0x16b9fd822093a2afb718017f56746a83daf174affcdd99a7fbd79cfa80e3cc00 \
  --chains.Base.signer.key 0x16b9fd822093a2afb718017f56746a83daf174affcdd99a7fbd79cfa80e3cc00 \
  --chains.Base.config /usr/src/hyperlane/config/Base.json
```

***

### **9️⃣ Check Logs**

```bash
docker logs -f hyperlane-agent-base
```

You may see a warning:

```
Please send tokens to your chain signer address to announce
```

***

### **🔟 Fund Your Validator**

Send ETH (\~0.00003 ETH) to your chain signer address to allow the validator to announce:

**Chain signer address:**

```
0x04c82c2ed0785229a6fe60476e450cf02606034a
```

* You can use **MetaMask**, **Alchemy**, or **Jumper bridge** for this.

Once funded, the validator should announce automatically and start validating messages.
