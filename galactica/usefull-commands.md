# Usefull Commands

### Service operations ⚙️ <a href="#service-operations" id="service-operations"></a>

Check logs

```bash
sudo journalctl -u galacticad -f
```

Start service

```bash
sudo systemctl start galacticad
```

Stop service

```bash
sudo systemctl stop galacticad
```

Restart service

```bash
sudo systemctl restart galacticad
```

Check service status

```bash
sudo systemctl status galacticad
```

Reload services

```bash
sudo systemctl daemon-reload
```

Enable Service

```bash
sudo systemctl enable galacticad
```

Disable Service

```bash
sudo systemctl disable galacticad
```

Sync info

```bash
galacticad status 2>&1 | jq .SyncInfo
```

Node info

```bash
galacticad status 2>&1 | jq .NodeInfo
```

Your node peer

```bash
echo $(galacticad tendermint show-node-id)'@'$(wget -qO- eth0.me)':'$(cat $HOME/.galactica/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```

### Key management <a href="#key-management" id="key-management"></a>

Add New Wallet

```bash
galacticad keys add $WALLET
```

Restore executing wallet

```bash
galacticad keys add $WALLET --recover
```

List All Wallets

```bash
galacticad keys list
```

Delete wallet

```bash
galacticad keys delete $WALLET
```

Check Balance

```bash
galacticad q bank balances $(galacticad keys show $WALLET -a)
```

Export Key (save to wallet.backup)

```bash
galacticad keys export $WALLET
```

View EVM Prived Key

```bash
galacticad keys unsafe-export-eth-key $WALLET
```

Import Key (restore from wallet.backup)

```bash
galacticad keys import $WALLET wallet.backup
```

### Tokens <a href="#tokens" id="tokens"></a>

Withdraw all rewards

```bash
galacticad tx distribution withdraw-all-rewards --from $WALLET --chain-id galactica_9302-1 --gas 200000 --gas-prices 10agnet
```

Withdraw rewards and commission from your validator

```bash
galacticad tx distribution withdraw-rewards $VALOPER_ADDRESS --from $WALLET --commission --chain-id galactica_9302-1 --gas 200000 --gas-prices 10agnet -y
```

Check your balance

```bash
galacticad query bank balances $WALLET_ADDRESS
```

Delegate to Yourself

```bash
galacticad tx staking delegate $(galacticad keys show $WALLET --bech val -a) 1000000agnet --from $WALLET --chain-id galactica_9302-1 --gas 200000 --gas-prices 10agnet -y
```

Delegate

```bash
galacticad tx staking delegate <TO_VALOPER_ADDRESS> 1000000agnet --from $WALLET --chain-id galactica_9302-1 --gas 200000 --gas-prices 10agnet -y
```

Redelegate Stake to Another Validator

```bash
galacticad tx staking redelegate $VALOPER_ADDRESS <TO_VALOPER_ADDRESS> 1000000agnet --from $WALLET --chain-id galactica_9302-1 --gas 200000 --gas-prices 10agnet -y
```

Unbond

```bash
galacticad tx staking unbond $(galacticad keys show $WALLET --bech val -a) 1000000agnet --from $WALLET --chain-id galactica_9302-1 --gas 200000 --gas-prices 10agnet -y
```

Transfer Funds

```bash
galacticad tx bank send $WALLET_ADDRESS <TO_WALLET_ADDRESS> 1000000agnet --gas 200000 --gas-prices 10agnet -y
```

### Validator operations <a href="#validator-operations" id="validator-operations"></a>

Create New Validator

```bash
galacticad tx staking create-validator \
--amount 1000000agnet \
--from $WALLET \
--commission-rate 0.1 \
--commission-max-rate 0.2 \
--commission-max-change-rate 0.01 \
--min-self-delegation 1 \
--pubkey $(galacticad tendermint show-validator) \
--moniker "$MONIKER" \
--identity "" \
--details "xxxxxxxxx" \
--chain-id galactica_9302-1 \
--gas 200000 --gas-prices 10agnet \
-y
```

Edit Existing Validator

```bash
galacticad tx staking edit-validator \
--commission-rate 0.1 \
--new-moniker "$MONIKER" \
--identity "" \
--details "xxxxxxxxxx" \
--from $WALLET \
--chain-id galactica_9302-1 \
--gas 200000 --gas-prices 10agnet \
-y
```

Validator info

```bash
galacticad status 2>&1 | jq .ValidatorInfo
```

Validator Details

```bash
galacticad q staking validator $(galacticad keys show $WALLET --bech val -a)
```

Jailing info

```bash
galacticad q slashing signing-info $(galacticad tendermint show-validator)
```

Slashing parameters

```bash
galacticad q slashing params
```

Unjail validator

```bash
galacticad tx slashing unjail --from $WALLET --chain-id galactica_9302-1 --gas 200000 --gas-prices 10agnet -y
```

Active Validators List

```bash
galacticad q staking validators -oj --limit=2000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " 	 " + .description.moniker' | sort -gr | nl
```

Check Validator key

```bash
[[ $(galacticad q staking validator $VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(galacticad status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "Your key status is ok" || echo -e "Your key status is error"
```

Signing info

```bash
galacticad q slashing signing-info $(galacticad tendermint show-validator)
```

### Governance <a href="#governance" id="governance"></a>

Create New Text Proposal

```bash
galacticad  tx gov submit-proposal \
--title "" \
--description "" \
--deposit 1000000agnet \
--type Text \
--from $WALLET \
--gas 200000 --gas-prices 10agnet \
-y 
```

Proposals List

```bash
galacticad query gov proposals
```
