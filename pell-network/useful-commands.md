# Useful commands

### Service operations ⚙️ <a href="#service-operations" id="service-operations"></a>

Check logs

```bash
sudo journalctl -u pellcored -fo cat
```

Start service

```bash
sudo systemctl start pellcored
```

Stop service

```bash
sudo systemctl stop pellcored
```

Restart service

```bash
sudo systemctl restart pellcored
```

Check service status

```bash
sudo systemctl status pellcored
```

Reload services

```bash
sudo systemctl daemon-reload
```

Enable Service

```bash
sudo systemctl enable pellcored
```

Disable Service

```bash
sudo systemctl disable pellcored
```

Node info

```bash
pellcored status 2>&1 | jq
```

Your node peer

```bash
echo $(pellcored tendermint show-node-id)'@'$(wget -qO- eth0.me)':'$(cat $HOME/.pellcored/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```

### Key management <a href="#key-management" id="key-management"></a>

Add New Wallet

```bash
pellcored keys add $WALLET
```

Restore executing wallet

```bash
pellcored keys add $WALLET --recover
```

List All Wallets

```bash
pellcored keys list
```

Delete wallet

```bash
pellcored keys delete $WALLET
```

Check Balance

```bash
pellcored q bank balances $WALLET_ADDRESS 
```

Export Key (save to wallet.backup)

```bash
pellcored keys export $WALLET
```

View EVM Prived Key

```bash
pellcored keys unsafe-export-eth-key $WALLET
```

Import Key (restore from wallet.backup)

```bash
pellcored keys import $WALLET wallet.backup
```

### Tokens <a href="#tokens" id="tokens"></a>

Withdraw all rewards

```bash
pellcored tx distribution withdraw-all-rewards --from $WALLET --chain-id ignite_186-1 --gas auto --gas-adjustment 1.5 
```

Withdraw rewards and commission from your validator

```bash
pellcored tx distribution withdraw-rewards $VALOPER_ADDRESS --from $WALLET --commission --chain-id ignite_186-1 --gas auto --gas-adjustment 1.5 -y 
```

Check your balance

```bash
pellcored query bank balances $WALLET_ADDRESS
```

Delegate to Yourself

```bash
pellcored tx staking delegate $(pellcored keys show $WALLET --bech val -a) 1000000apell --from $WALLET --chain-id ignite_186-1 --gas auto --gas-adjustment 1.5 -y 
```

Delegate

```bash
pellcored tx staking delegate <TO_VALOPER_ADDRESS> 1000000apell --from $WALLET --chain-id ignite_186-1 --gas auto --gas-adjustment 1.5 -y 	
```

Redelegate Stake to Another Validator

```bash
pellcored tx staking redelegate $VALOPER_ADDRESS <TO_VALOPER_ADDRESS> 1000000apell --from $WALLET --chain-id ignite_186-1 --gas auto --gas-adjustment 1.5 -y 
```

Unbond

```bash
pellcored tx staking unbond $(pellcored keys show $WALLET --bech val -a) 1000000apell --from $WALLET --chain-id ignite_186-1 --gas auto --gas-adjustment 1.5 -y 
```

Transfer Funds

```bash
pellcored tx bank send $WALLET_ADDRESS <TO_WALLET_ADDRESS> 1000000apell --gas auto --gas-adjustment 1.5 -y 
```

### Validator operations <a href="#validator-operations" id="validator-operations"></a>

Create New Validator

```bash
pellcored tx staking create-validator \
--amount 1000000apell \
--from $WALLET \
--commission-rate 0.1 \
--commission-max-rate 0.2 \
--commission-max-change-rate 0.01 \
--min-self-delegation 1 \
--pubkey $(pellcored tendermint show-validator) \
--moniker "$MONIKER" \
--identity "" \
--details "xxxxxxxx" \
--chain-id ignite_186-1 \
--gas auto --gas-adjustment 1.5 \
-y 
```

Edit Existing Validator

```bash
pellcored tx staking edit-validator \
--commission-rate 0.1 \
--new-moniker "$MONIKER" \
--identity "" \
--details "xxxxxxxx" \
--from $WALLET \
--chain-id ignite_186-1 \
--gas auto --gas-adjustment 1.5 \
-y 
```

Validator info

```bash
pellcored status 2>&1 | jq
```

Validator Details

```bash
pellcored q staking validator $(pellcored keys show $WALLET --bech val -a) 
```

Jailing info

```bash
pellcored q slashing signing-info $(pellcored tendermint show-validator) 
```

Slashing parameters

```bash
pellcored q slashing params 
```

Unjail validator

```bash
pellcored tx slashing unjail --from $WALLET --chain-id ignite_186-1 --gas auto --gas-adjustment 1.5 -y 
```

Active Validators List

```bash
pellcored q staking validators -oj --limit=2000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " 	 " + .description.moniker' | sort -gr | nl 
```

Check Validator key

```bash
[[ $(pellcored q staking validator $VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(pellcored status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "Your key status is ok" || echo -e "Your key status is error"
```

Signing info

```bash
pellcored q slashing signing-info $(pellcored tendermint show-validator) 
```

### Governance <a href="#governance" id="governance"></a>

Create New Text Proposal

```bash
pellcored  tx gov submit-proposal \
--title "" \
--description "" \
--deposit 1000000apell \
--type Text \
--from $WALLET \
--gas auto --gas-adjustment 1.5 \
-y 
```

Proposals List

```bash
pellcored query gov proposals 
```

View proposal

```bash
pellcored query gov proposal 1 
```

Vote

```bash
pellcored tx gov vote 1 yes --from $WALLET --chain-id ignite_186-1  --gas auto --gas-adjustment 1.5 -y 
```
