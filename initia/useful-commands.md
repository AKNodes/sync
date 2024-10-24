# Useful Commands

### Service operations ⚙️ <a href="#service-operations" id="service-operations"></a>

Check logs

```bash
sudo journalctl -u initiad -f
```

Start service

```bash
sudo systemctl start initiad
```

Stop service

```bash
sudo systemctl stop initiad
```

Restart service

```bash
sudo systemctl restart initiad
```

Check service status

```bash
sudo systemctl status initiad
```

Reload services

```bash
sudo systemctl daemon-reload
```

Enable Service

```bash
sudo systemctl enable initiad
```

Disable Service

```bash
sudo systemctl disable initiad
```

Node info

```bash
initiad status 2>&1 | jq
```

Your node peer

```bash
echo $(initiad tendermint show-node-id)'@'$(wget -qO- eth0.me)':'$(cat $HOME/.initia/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```

### Key management <a href="#key-management" id="key-management"></a>

Add New Wallet

```bash
initiad keys add $WALLET
```

Restore executing wallet

```bash
initiad keys add $WALLET --recover
```

List All Wallets

```bash
initiad keys list
```

Delete wallet

```bash
initiad keys delete $WALLET
```

Check Balance

```bash
initiad q bank balances $WALLET_ADDRESS 
```

Export Key (save to wallet.backup)

```bash
initiad keys export $WALLET
```

View EVM Prived Key

```bash
initiad keys unsafe-export-eth-key $WALLET
```

Import Key (restore from wallet.backup)

```bash
initiad keys import $WALLET wallet.backup
```

### Tokens <a href="#tokens" id="tokens"></a>

Withdraw all rewards

```bash
initiad tx distribution withdraw-all-rewards --from $WALLET --chain-id initiation-1 --gas auto --fees 80000uinit 
```

Withdraw rewards and commission from your validator

```bash
initiad tx distribution withdraw-rewards $VALOPER_ADDRESS --from $WALLET --commission --chain-id initiation-1 --gas auto --fees 80000uinit -y 
```

Check your balance

```bash
initiad query bank balances $WALLET_ADDRESS
```

Delegate to Yourself

```bash
initiad tx staking delegate $(initiad keys show $WALLET --bech val -a) 1000000uinit --from $WALLET --chain-id initiation-1 --gas auto --fees 80000uinit -y 
```

Delegate

```bash
initiad tx staking delegate <TO_VALOPER_ADDRESS> 1000000uinit --from $WALLET --chain-id initiation-1 --gas auto --fees 80000uinit -y 	
```

Redelegate Stake to Another Validator

```bash
initiad tx staking redelegate $VALOPER_ADDRESS <TO_VALOPER_ADDRESS> 1000000uinit --from $WALLET --chain-id initiation-1 --gas auto --fees 80000uinit -y 
```

Unbond

```bash
initiad tx staking unbond $(initiad keys show $WALLET --bech val -a) 1000000uinit --from $WALLET --chain-id initiation-1 --gas auto --fees 80000uinit -y 
```

Transfer Funds

```bash
initiad tx bank send $WALLET_ADDRESS <TO_WALLET_ADDRESS> 1000000uinit --gas auto --fees 80000uinit -y 
```

### Validator operations <a href="#validator-operations" id="validator-operations"></a>

Create New Validator

```bash
initiad tx staking create-validator \
--amount 1000000uinit \
--from $WALLET \
--commission-rate 0.1 \
--commission-max-rate 0.2 \
--commission-max-change-rate 0.01 \
--min-self-delegation 1 \
--pubkey $(initiad tendermint show-validator) \
--moniker "$MONIKER" \
--identity "" \
--details "I love blockchain ❤️" \
--chain-id initiation-1 \
--gas auto --fees 80000uinit \
-y 
```

Edit Existing Validator

```bash
initiad tx staking edit-validator \
--commission-rate 0.1 \
--new-moniker "$MONIKER" \
--identity "" \
--details "I love blockchain ❤️" \
--from $WALLET \
--chain-id initiation-1 \
--gas auto --fees 80000uinit \
-y 
```

Validator info

```bash
initiad status 2>&1 | jq
```

Validator Details

```bash
initiad q staking validator $(initiad keys show $WALLET --bech val -a) 
```

Jailing info

```bash
initiad q slashing signing-info $(initiad tendermint show-validator) 
```

Slashing parameters

```bash
initiad q slashing params 
```

Unjail validator

```bash
initiad tx slashing unjail --from $WALLET --chain-id initiation-1 --gas auto --fees 80000uinit -y 
```

Active Validators List

```bash
initiad q staking validators -oj --limit=2000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " 	 " + .description.moniker' | sort -gr | nl 
```

Check Validator key

```bash
[[ $(initiad q staking validator $VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(initiad status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "Your key status is ok" || echo -e "Your key status is error"
```

Signing info

```bash
initiad q slashing signing-info $(initiad tendermint show-validator) 
```

### Governance <a href="#governance" id="governance"></a>

Create New Text Proposal

```bash
initiad  tx gov submit-proposal \
--title "" \
--description "" \
--deposit 1000000uinit \
--type Text \
--from $WALLET \
--gas auto --fees 80000uinit \
-y 
```

Proposals List

```bash
initiad query gov proposals 
```

View proposal

```bash
initiad query gov proposal 1 
```

Vote

```bash
initiad tx gov vote 1 yes --from $WALLET --chain-id initiation-1  --gas auto --fees 80000uinit -y 
```
