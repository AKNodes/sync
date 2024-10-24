# Useful commands

### <mark style="color:orange;">Service operations ⚙️</mark> <a href="#service-operations" id="service-operations"></a>

Check logs

```bash
sudo journalctl -u wardend -f
```

Start service

```bash
sudo systemctl start wardend
```

Stop service

```bash
sudo systemctl stop wardend
```

Restart service

```bash
sudo systemctl restart wardend
```

Check service status

```bash
sudo systemctl status wardend
```

Reload services

```bash
sudo systemctl daemon-reload
```

Enable Service

```bash
sudo systemctl enable wardend
```

Disable Service

```bash
sudo systemctl disable wardend
```

Node info

```bash
wardend status 2>&1 | jq
```

Your node peer

```bash
echo $(wardend tendermint show-node-id)'@'$(wget -qO- eth0.me)':'$(cat $HOME/.warden/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```

### <mark style="color:yellow;">Key management</mark> <a href="#key-management" id="key-management"></a>

Add New Wallet

```bash
wardend keys add $WALLET
```

Restore executing wallet

```bash
wardend keys add $WALLET --recover
```

List All Wallets

```bash
wardend keys list
```

Delete wallet

```bash
wardend keys delete $WALLET
```

Check Balance

```bash
wardend q bank balances $WALLET_ADDRESS 
```

Export Key (save to wallet.backup)

```bash
wardend keys export $WALLET
```

View EVM Prived Key

```bash
wardend keys unsafe-export-eth-key $WALLET
```

Import Key (restore from wallet.backup)

```bash
wardend keys import $WALLET wallet.backup
```

### <mark style="color:green;">Tokens</mark> <a href="#tokens" id="tokens"></a>

Withdraw all rewards

```bash
wardend tx distribution withdraw-all-rewards --from $WALLET --chain-id chiado_10010-1 --gas auto --gas-adjustment 1.6 --fees 250000000000000award 
```

Withdraw rewards and commission from your validator

```bash
wardend tx distribution withdraw-rewards $VALOPER_ADDRESS --from $WALLET --commission --chain-id chiado_10010-1 --gas auto --gas-adjustment 1.6 --fees 250000000000000award -y 
```

Check your balance

```bash
wardend query bank balances $WALLET_ADDRESS
```

Delegate to Yourself

```bash
wardend tx staking delegate $(wardend keys show $WALLET --bech val -a) 1000000award --from $WALLET --chain-id chiado_10010-1 --gas auto --gas-adjustment 1.6 --fees 250000000000000award -y 
```

Delegate

```bash
wardend tx staking delegate <TO_VALOPER_ADDRESS> 1000000award --from $WALLET --chain-id chiado_10010-1 --gas auto --gas-adjustment 1.6 --fees 250000000000000award -y 	
```

Redelegate Stake to Another Validator

```bash
wardend tx staking redelegate $VALOPER_ADDRESS <TO_VALOPER_ADDRESS> 1000000award --from $WALLET --chain-id chiado_10010-1 --gas auto --gas-adjustment 1.6 --fees 250000000000000award -y 
```

Unbond

```bash
wardend tx staking unbond $(wardend keys show $WALLET --bech val -a) 1000000award --from $WALLET --chain-id chiado_10010-1 --gas auto --gas-adjustment 1.6 --fees 250000000000000award -y 
```

Transfer Funds

```bash
wardend tx bank send $WALLET_ADDRESS <TO_WALLET_ADDRESS> 1000000award --gas auto --gas-adjustment 1.6 --fees 250000000000000award -y 
```

### <mark style="color:purple;">Validator operations</mark> <a href="#validator-operations" id="validator-operations"></a>

Create New Validator

```bash
wardend tx staking create-validator \
--amount 1000000award \
--from $WALLET \
--commission-rate 0.1 \
--commission-max-rate 0.2 \
--commission-max-change-rate 0.01 \
--min-self-delegation 1 \
--pubkey $(wardend tendermint show-validator) \
--moniker "$MONIKER" \
--identity "" \
--details "xxxxxx" \
--chain-id chiado_10010-1 \
--gas auto --gas-adjustment 1.6 --fees 250000000000000award \
-y 
```

Edit Existing Validator

```bash
wardend tx staking edit-validator \
--commission-rate 0.1 \
--new-moniker "$MONIKER" \
--identity "" \
--details "xxxxxx" \
--from $WALLET \
--chain-id chiado_10010-1 \
--gas auto --gas-adjustment 1.6 --fees 250000000000000award \
-y 
```

Validator info

```bash
wardend status 2>&1 | jq
```

Validator Details

```bash
wardend q staking validator $(wardend keys show $WALLET --bech val -a) 
```

Jailing info

```bash
wardend q slashing signing-info $(wardend tendermint show-validator) 
```

Slashing parameters

```bash
wardend q slashing params 
```

Unjail validator

```bash
wardend tx slashing unjail --from $WALLET --chain-id chiado_10010-1 --gas auto --gas-adjustment 1.6 --fees 250000000000000award -y 
```

Active Validators List

```bash
wardend q staking validators -oj --limit=2000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " 	 " + .description.moniker' | sort -gr | nl 
```

Check Validator key

```bash
[[ $(wardend q staking validator $VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(wardend status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "Your key status is ok" || echo -e "Your key status is error"
```

Signing info

```bash
wardend q slashing signing-info $(wardend tendermint show-validator) 
```

### <mark style="color:blue;background-color:purple;">Governance</mark> <a href="#governance" id="governance"></a>

Create New Text Proposal

```bash
wardend  tx gov submit-proposal \
--title "" \
--description "" \
--deposit 1000000award \
--type Text \
--from $WALLET \
--gas auto --gas-adjustment 1.6 --fees 250000000000000award \
-y 
```

Proposals List

```bash
wardend query gov proposals 
```

View proposal

```bash
wardend query gov proposal 1 
```

Vote

```bash
wardend tx gov vote 1 yes --from $WALLET --chain-id chiado_10010-1  --gas auto --gas-adjustment 1.6 --fees 250000000000000award -y 
```
