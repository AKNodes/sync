# Useful commands

#### 🚀 **Service Operations ⚙️**

```bash
# Check logs
sudo journalctl -u arkeod -f

# Start service
sudo systemctl start arkeod

# Stop service
sudo systemctl stop arkeod

# Restart service
sudo systemctl restart arkeod

# Check service status
sudo systemctl status arkeod

# Reload services
sudo systemctl daemon-reload

# Enable Service
sudo systemctl enable arkeod

# Disable Service
sudo systemctl disable arkeod
```

***

#### 📡 **Node Info**

```bash
sync info
arkeod status 2>&1 | jq .SyncInfo

# Node info
arkeod status 2>&1 | jq .NodeInfo

# Your node peer
echo $(arkeod tendermint show-node-id)'@'$(wget -qO- eth0.me)':'$(cat $HOME/.arkeo/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```

***

#### 🔐 **Key Management**

```bash
Add New Key
arkeod keys add wallet

# Recover Existing Key
arkeod keys add wallet --recover

# List All Keys
arkeod keys list

# Delete Key
arkeod keys delete wallet

# Export Key
arkeod keys export wallet

# Import Key
arkeod keys import wallet wallet.backup
```

***

#### 💰 **Wallet & Token Commands**

```bash
Query Wallet Balance
arkeod q bank balances $(arkeod keys show wallet -a)

# Check your balance
arkeod query bank balances $WALLET_ADDRESS

# Withdraw all rewards
arkeod tx distribution withdraw-all-rewards \
--from $WALLET \
--chain-id arkeo-main-v1 \
--fees 0.001uarkeo \
--gas auto --gas-adjustment 1.5 -y

# Withdraw rewards + commission
arkeod tx distribution withdraw-rewards $VALOPER_ADDRESS \
--from $WALLET \
--commission \
--chain-id arkeo-main-v1 \
--fees 0.001uarkeo \
--gas auto --gas-adjustment 1.5 -y
```

***

#### 🤝 **Staking & Validator Ops**

```bash
arkeod tx staking delegate $(arkeod keys show $WALLET --bech val -a) 1000000uarkeo \
--from $WALLET \
--chain-id arkeo-main-v1 \
--fees 0.001uarkeo \
--gas auto --gas-adjustment 1.5 -y

# Delegate to another validator
arkeod tx staking delegate <TO_VALOPER_ADDRESS> 1000000uarkeo \
--from $WALLET \
--chain-id arkeo-main-v1 \
--fees 0.001uarkeo \
--gas auto --gas-adjustment 1.5 -y

# Redelegate
arkeod tx staking redelegate $VALOPER_ADDRESS <TO_VALOPER_ADDRESS> 1000000uarkeo \
--from $WALLET \
--chain-id arkeo-main-v1 \
--fees 0.001uarkeo \
--gas auto --gas-adjustment 1.5 -y

# Unbond
arkeod tx staking unbond $(arkeod keys show $WALLET --bech val -a) 1000000uarkeo \
--from $WALLET \
--chain-id arkeo-main-v1 \
--fees 0.001uarkeo \
--gas auto --gas-adjustment 1.5 -y

# Transfer Funds
arkeod tx bank send $WALLET_ADDRESS <TO_WALLET_ADDRESS> 1000000uarkeo \
--chain-id arkeo-main-v1 \
--fees 0.001uarkeo \
--gas auto --gas-adjustment 1.5 -y
```

***

#### 🏗️ **Validator Management**

```bash
arkeod tx staking create-validator \
--amount 1000000uarkeo \
--from $WALLET \
--commission-rate 0.1 \
--commission-max-rate 0.2 \
--commission-max-change-rate 0.01 \
--min-self-delegation 1 \
--pubkey $(arkeod tendermint show-validator) \
--moniker "$MONIKER" \
--identity "" \
--details "xxxxxxxxxxxx" \
--chain-id arkeo-main-v1 \
--fees 0.001uarkeo \
--gas auto --gas-adjustment 1.5 -y

# Edit Validator
arkeod tx staking edit-validator \
--commission-rate 0.1 \
--new-moniker "$MONIKER" \
--identity "" \
--details "xxxxxxxxxx" \
--from $WALLET \
--chain-id arkeo-main-v1 \
--fees 0.001uarkeo \
--gas auto --gas-adjustment 1.5 -y

# Validator Info
arkeod status 2>&1 | jq .ValidatorInfo

# Validator Details
arkeod q staking validator $(arkeod keys show $WALLET --bech val -a)

# Jailing info
arkeod q slashing signing-info $(arkeod tendermint show-validator)

# Unjail Validator
arkeod tx slashing unjail \
--from $WALLET \
--chain-id arkeo-main-v1 \
--fees 0.001uarkeo \
--gas auto --gas-adjustment 1.5 -y
```

***

#### 🧠 **Governance**

```bash
arkeod tx gov submit-proposal \
--title "Proposal Title" \
--description "Proposal Description" \
--deposit 1000000uarkeo \
--type Text \
--from $WALLET \
--chain-id arkeo-main-v1 \
--fees 0.001uarkeo \
--gas auto --gas-adjustment 1.5 -y

# View Proposals
arkeod query gov proposals

# View Specific Proposal
arkeod query gov proposal 1

# Vote
arkeod tx gov vote 1 yes \
--from $WALLET \
--chain-id arkeo-main-v1 \
--fees 0.001uarkeo \
--gas auto --gas-adjustment 1.5 -y
```

***

#### 🧹 **Delete Node**

```bash
bashKopiërenBewerkensudo systemctl stop arkeod
sudo systemctl disable arkeod
sudo rm -rf /etc/systemd/system/arkeod.service
sudo rm $(which arkeod)
sudo rm -rf $HOME/.arkeo
sed -i "/ARKEO_/d" $HOME/.bash_profile
```
