# Usefull commands

### Service Operations ⚙️

```
# Check logs
sudo journalctl -u mantrachaind -f

# Start service
sudo systemctl start mantrachaind

# Stop service
sudo systemctl stop mantrachaind

# Restart service
sudo systemctl restart mantrachaind

# Check service status
sudo systemctl status mantrachaind

# Reload services
sudo systemctl daemon-reload

# Enable Service
sudo systemctl enable mantrachaind

# Disable Service
sudo systemctl disable mantrachaind
```

***

### Node Info 🌐

```
# Node info
mantrachaind status 2>&1 | jq

# Your node peer
echo $(mantrachaind tendermint show-node-id)'@'$(wget -qO- eth0.me)':'$(cat $HOME/.mantrachain/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```

***

### Key Management 🔑

```
# Add New Wallet
mantrachaind keys add $WALLET

# Restore existing wallet
mantrachaind keys add $WALLET --recover

# List All Wallets
mantrachaind keys list

# Delete wallet
mantrachaind keys delete $WALLET

# Check Balance
mantrachaind q bank balances $WALLET_ADDRESS 

# Export Key (save to wallet.backup)
mantrachaind keys export $WALLET

# View EVM Private Key
mantrachaind keys unsafe-export-eth-key $WALLET

# Import Key (restore from wallet.backup)
mantrachaind keys import $WALLET wallet.backup
```

***

### Tokens 💰

```
# Withdraw all rewards
mantrachaind tx distribution withdraw-all-rewards --from $WALLET --chain-id mantra-1 --gas auto --gas-adjustment 1.5 --fees 20000000000000000amantra

# Withdraw rewards and commission from your validator
mantrachaind tx distribution withdraw-rewards $VALOPER_ADDRESS --from $WALLET --commission --chain-id mantra-1 --gas auto --gas-adjustment 1.5 --fees 20000000000000000amantra -y

# Check your balance
mantrachaind query bank balances $WALLET_ADDRESS
```

***

### Staking & Delegation 🌱

```
# Delegate to yourself
mantrachaind tx staking delegate $(mantrachaind keys show $WALLET --bech val -a) 20000000000000000amantra \
  --from $WALLET --chain-id mantra-1 --gas auto --gas-adjustment 1.5 --fees 20000000000000000amantra -y

# Delegate to another validator
mantrachaind tx staking delegate <TO_VALOPER_ADDRESS> 20000000000000000amantra \
  --from $WALLET --chain-id mantra-1 --gas auto --gas-adjustment 1.5 --fees 20000000000000000amantra -y

# Redelegate stake to another validator
mantrachaind tx staking redelegate $VALOPER_ADDRESS <TO_VALOPER_ADDRESS> 20000000000000000amantra \
  --from $WALLET --chain-id mantra-1 --gas auto --gas-adjustment 1.5 --fees 20000000000000000amantra -y

# Unbond
mantrachaind tx staking unbond $(mantrachaind keys show $WALLET --bech val -a) 20000000000000000amantra \
  --from $WALLET --chain-id mantra-1 --gas auto --gas-adjustment 1.5 --fees 20000000000000000amantra -y

# Transfer Funds
mantrachaind tx bank send $WALLET_ADDRESS <TO_WALLET_ADDRESS> 20000000000000000amantra \
  --gas auto --gas-adjustment 1.5 --fees 20000000000000000amantra -y
```

***

### Validator Operations 🏛️

```
# Create New Validator
mantrachaind tx staking create-validator \
--amount 20000000000000000amantra \
--from $WALLET \
--commission-rate 0.1 \
--commission-max-rate 0.2 \
--commission-max-change-rate 0.01 \
--min-self-delegation 1 \
--pubkey $(mantrachaind tendermint show-validator) \
--moniker "$MONIKER" \
--identity "" \
--details "" \
--chain-id mantra-1 \
--gas auto --gas-adjustment 1.5 --fees 20000000000000000amantra \
-y

# Edit Existing Validator
mantrachaind tx staking edit-validator \
--commission-rate 0.1 \
--new-moniker "$MONIKER" \
--identity "" \
--details "" \
--from $WALLET \
--chain-id mantra-1 \
--gas auto --gas-adjustment 1.5 --fees 20000000000000000amantra \
-y

# Validator info
mantrachaind status 2>&1 | jq

# Validator Details
mantrachaind q staking validator $(mantrachaind keys show $WALLET --bech val -a) 

# Jailing info
mantrachaind q slashing signing-info $(mantrachaind tendermint show-validator) 

# Slashing parameters
mantrachaind q slashing params 

# Unjail validator
mantrachaind tx slashing unjail --from $WALLET --chain-id mantra-1 --gas auto --gas-adjustment 1.5 --fees 20000000000000000amantra -y

# Active Validators List
mantrachaind q staking validators -oj --limit=2000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " 	 " + .description.moniker' | sort -gr | nl

# Check Validator key
[[ $(mantrachaind q staking validator $VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(mantrachaind status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "Your key status is ok" || echo -e "Your key status is error"

# Signing info
mantrachaind q slashing signing-info $(mantrachaind tendermint show-validator) 
```

***

### Governance 🗳️

```
# Create New Text Proposal
mantrachaind  tx gov submit-proposal \
--title "" \
--description "" \
--deposit 20000000000000000amantra \
--type Text \
--from $WALLET \
--gas auto --gas-adjustment 1.5 --fees 20000000000000000amantra \
-y

# Proposals List
mantrachaind query gov proposals 

# View proposal
mantrachaind query gov proposal 1 

# Vote
mantrachaind tx gov vote 1 yes --from $WALLET --chain-id mantra-1 --gas auto --gas-adjustment 1.5 --f
```
