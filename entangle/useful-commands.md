# Useful commands

### <mark style="color:blue;">Service operations</mark> ⚙️ <a href="#service-operations" id="service-operations"></a>

Check logs

```bash
sudo journalctl -u entangled -f
```

Start service

```bash
sudo systemctl start entangled
```

Stop service

```bash
sudo systemctl stop entangled
```

Restart service

```bash
sudo systemctl restart entangled
```

Check service status

```bash
sudo systemctl status entangled
```

Reload services

```bash
sudo systemctl daemon-reload
```

Enable Service

```bash
sudo systemctl enable entangled
```

Disable Service

```bash
sudo systemctl disable entangled
```

Sync info

```bash
entangled status 2>&1 | jq .SyncInfo
```

Node info

```bash
entangled status 2>&1 | jq .NodeInfo
```

Your node peer

```bash
echo $(entangled tendermint show-node-id)'@'$(wget -qO- eth0.me)':'$(cat $HOME/.entangled/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```

### <mark style="color:purple;">Key management</mark> <a href="#key-management" id="key-management"></a>

Add New Wallet

```bash
entangled keys add $WALLET
```

Restore executing wallet

```bash
entangled keys add $WALLET --recover
```

List All Wallets

```bash
entangled keys list
```

Delete wallet

```bash
entangled keys delete $WALLET
```

Check Balance

```bash
entangled q bank balances $(entangled keys show $WALLET -a)
```

Export Key (save to wallet.backup)

```bash
entangled keys export $WALLET
```

Import Key (restore from wallet.backup)

```bash
entangled keys import $WALLET wallet.backup
```

### <mark style="color:orange;">Tokens</mark> <a href="#tokens" id="tokens"></a>

Withdraw all rewards

```bash
entangled tx distribution withdraw-all-rewards --from $WALLET --chain-id entangle_33133-1 --gas=700000 --gas-prices="20aNGL"
```

Withdraw rewards and commission from your validator

```bash
entangled tx distribution withdraw-rewards $VALOPER_ADDRESS --from $WALLET --commission --chain-id entangle_33133-1 --gas=700000 --gas-prices="20aNGL" -y
```

Check your balance

```bash
entangled query bank balances $WALLET_ADDRESS
```

Delegate to Yourself

```bash
entangled tx staking delegate $(entangled keys show $WALLET --bech val -a) 1000000aNGL --from $WALLET --chain-id entangle_33133-1 --gas=700000 --gas-prices="20aNGL" -y
```

Delegate

```bash
entangled tx staking delegate <TO_VALOPER_ADDRESS> 1000000aNGL --from $WALLET --chain-id entangle_33133-1 --gas=700000 --gas-prices="20aNGL" -y
```

Redelegate Stake to Another Validator

```bash
entangled tx staking redelegate $VALOPER_ADDRESS <TO_VALOPER_ADDRESS> 1000000aNGL --from $WALLET --chain-id entangle_33133-1 --gas=700000 --gas-prices="20aNGL" -y
```

Unbond

```bash
entangled tx staking unbond $(entangled keys show $WALLET --bech val -a) 1000000aNGL --from $WALLET --chain-id entangle_33133-1 --gas=700000 --gas-prices="20aNGL" -y
```

Transfer Funds

```bash
entangled tx bank send $WALLET_ADDRESS <TO_WALLET_ADDRESS> 1000000aNGL --gas=700000 --gas-prices="20aNGL" -y
```

### <mark style="color:red;">Validator operations</mark> <a href="#validator-operations" id="validator-operations"></a>

Create New Validator

```bash
entangled tx staking create-validator \
--amount 1000000aNGL \
--from $WALLET \
--commission-rate 0.1 \
--commission-max-rate 0.2 \
--commission-max-change-rate 0.01 \
--min-self-delegation 1 \
--pubkey $(entangled tendermint show-validator) \
--moniker "$MONIKER" \
--identity "" \
--details "xxxxxxxxx" \
--chain-id entangle_33133-1 \
--gas=700000 --gas-prices="20aNGL" \
-y
```

Edit Existing Validator

```bash
entangled tx staking edit-validator \
--commission-rate 0.1 \
--new-moniker "$MONIKER" \
--identity "" \
--details "xxxxxxxxxxxxx" \
--from $WALLET \
--chain-id entangle_33133-1 \
--gas=700000 --gas-prices="20aNGL" \
-y
```

Validator info

```bash
entangled status 2>&1 | jq .ValidatorInfo
```

Validator Details

```bash
entangled q staking validator $(entangled keys show $WALLET --bech val -a)
```

Jailing info

```bash
entangled q slashing signing-info $(entangled tendermint show-validator)
```

Unjail validator

```bash
entangled tx slashing unjail --from $WALLET --chain-id entangle_33133-1 --gas=700000 --gas-prices="20aNGL" -y
```

Active Validators List

```bash
entangled q staking validators -oj --limit=2000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " 	 " + .description.moniker' | sort -gr | nl
```

Check Validator key

```bash
[[ $(entangled q staking validator $VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(entangled status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "Your key status is ok" || echo -e "Your key status is error"
```

Signing info

```bash
entangled q slashing signing-info $(entangled tendermint show-validator)
```

### <mark style="background-color:yellow;">Governance</mark> <a href="#governance" id="governance"></a>

Create New Text Proposal

```bash
entangled  tx gov submit-proposal \
--title "" \
--description "" \
--deposit 1000000aNGL \
--type Text \
--from $WALLET \
--gas=700000 --gas-prices="20aNGL" \
-y 
```

Proposals List

```bash
entangled query gov proposals
```

Proposal ID

View proposal

```bash
entangled query gov proposal 1
```

Vote

```bash
entangled tx gov vote 1 yes --from $WALLET --chain-id entangle_33133-1  --gas=700000 --gas-prices="20aNGL" -y
```
