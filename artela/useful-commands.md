# Useful commands

### Service operations ⚙️ <a href="#service-operations" id="service-operations"></a>

Check logs

```bash
sudo journalctl -u artelad -f
```

Start service

```bash
sudo systemctl start artelad
```

Stop service

```bash
sudo systemctl stop artelad
```

Restart service

```bash
sudo systemctl restart artelad
```

Check service status

```bash
sudo systemctl status artelad
```

Reload services

```bash
sudo systemctl daemon-reload
```

Enable Service

```bash
sudo systemctl enable artelad
```

Disable Service

```bash
sudo systemctl disable artelad
```

Sync info

```bash
artelad status 2>&1 | jq .SyncInfo
```

Node info

```bash
artelad status 2>&1 | jq .NodeInfo
```

Your node peer

```bash
echo $(artelad tendermint show-node-id)'@'$(wget -qO- eth0.me)':'$(cat $HOME/.artelad/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```

### Key management <a href="#key-management" id="key-management"></a>

Add New Wallet

```bash
artelad keys add $WALLET
```

Restore executing wallet

```bash
artelad keys add $WALLET --recover
```

List All Wallets

```bash
artelad keys list
```

Delete wallet

```bash
artelad keys delete $WALLET
```

Check Balance

```bash
artelad q bank balances $(artelad keys show $WALLET -a)
```

Export Key (save to wallet.backup)

```bash
artelad keys export $WALLET
```

View EVM Prived Key

```bash
artelad keys unsafe-export-eth-key $WALLET
```

Import Key (restore from wallet.backup)

```bash
artelad keys import $WALLET wallet.backup
```

### Tokens <a href="#tokens" id="tokens"></a>

Withdraw all rewards

```bash
artelad tx distribution withdraw-all-rewards --from $WALLET --chain-id artela_11822-1 --gas auto --gas-adjustment 1.5
```

Withdraw rewards and commission from your validator

```bash
artelad tx distribution withdraw-rewards $VALOPER_ADDRESS --from $WALLET --commission --chain-id artela_11822-1 --gas auto --gas-adjustment 1.5 -y
```

Check your balance

```bash
artelad query bank balances $WALLET_ADDRESS
```

Delegate to Yourself

```bash
artelad tx staking delegate $(artelad keys show $WALLET --bech val -a) 1000000uart --from $WALLET --chain-id artela_11822-1 --gas auto --gas-adjustment 1.5 -y
```

Delegate

```bash
artelad tx staking delegate <TO_VALOPER_ADDRESS> 1000000uart --from $WALLET --chain-id artela_11822-1 --gas auto --gas-adjustment 1.5 -y
```

Redelegate Stake to Another Validator

```bash
artelad tx staking redelegate $VALOPER_ADDRESS <TO_VALOPER_ADDRESS> 1000000uart --from $WALLET --chain-id artela_11822-1 --gas auto --gas-adjustment 1.5 -y
```

Unbond

```bash
artelad tx staking unbond $(artelad keys show $WALLET --bech val -a) 1000000uart --from $WALLET --chain-id artela_11822-1 --gas auto --gas-adjustment 1.5 -y
```

Transfer Funds

```bash
artelad tx bank send $WALLET_ADDRESS <TO_WALLET_ADDRESS> 1000000uart --gas auto --gas-adjustment 1.5 -y
```

### Validator operations <a href="#validator-operations" id="validator-operations"></a>

Create New Validator

```bash
artelad tx staking create-validator \
--amount 1000000uart \
--from $WALLET \
--commission-rate 0.1 \
--commission-max-rate 0.2 \
--commission-max-change-rate 0.01 \
--min-self-delegation 1 \
--pubkey $(artelad tendermint show-validator) \
--moniker "$MONIKER" \
--identity "" \
--details "" \
--chain-id artela_11822-1 \
--gas auto --gas-adjustment 1.5 \
-y
```

Edit Existing Validator

```bash
artelad tx staking edit-validator \
--commission-rate 0.1 \
--new-moniker "$MONIKER" \
--identity "" \
--details "" \
--from $WALLET \
--chain-id artela_11822-1 \
--gas auto --gas-adjustment 1.5 \
-y
```

Validator info

```bash
artelad status 2>&1 | jq .ValidatorInfo
```

Validator Details

```bash
artelad q staking validator $(artelad keys show $WALLET --bech val -a)
```

Jailing info

```bash
artelad q slashing signing-info $(artelad tendermint show-validator)
```

Slashing parameters

```bash
artelad q slashing params
```

Unjail validator

```bash
artelad tx slashing unjail --from $WALLET --chain-id artela_11822-1 --gas auto --gas-adjustment 1.5 -y
```

Active Validators List

```bash
artelad q staking validators -oj --limit=2000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " 	 " + .description.moniker' | sort -gr | nl
```

Check Validator key

```bash
[[ $(artelad q staking validator $VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(artelad status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "Your key status is ok" || echo -e "Your key status is error"
```

Signing info

```bash
artelad q slashing signing-info $(artelad tendermint show-validator)
```

### Governance <a href="#governance" id="governance"></a>

Create New Text Proposal

```bash
artelad  tx gov submit-proposal \
--title "" \
--description "" \
--deposit 1000000uart \
--type Text \
--from $WALLET \
--gas auto --gas-adjustment 1.5 \
-y 
```

Proposals List

```bash
artelad query gov proposals
```

View proposal

```bash
artelad query gov proposal 1
```

Vote

```bash
artelad tx gov vote 1 yes --from $WALLET --chain-id artela_11822-1  --gas auto --gas-adjustment 1.5 -y
```
