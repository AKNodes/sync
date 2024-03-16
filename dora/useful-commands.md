# Useful commands

### Service operations ⚙️ <a href="#service-operations" id="service-operations"></a>

Check logs

```bash
sudo journalctl -u dorad -f
```

Start service

```bash
sudo systemctl start dorad
```

Stop service

```bash
sudo systemctl stop dorad
```

Restart service

```bash
sudo systemctl restart dorad
```

Check service status

```bash
sudo systemctl status dorad
```

Reload services

```bash
sudo systemctl daemon-reload
```

Enable Service

```bash
sudo systemctl enable dorad
```

Disable Service

```bash
sudo systemctl disable dorad
```

Sync info

```bash
dorad status 2>&1 | jq .SyncInfo
```

Node info

```bash
dorad status 2>&1 | jq .NodeInfo
```

Your node peer

```bash
echo $(dorad tendermint show-node-id)'@'$(wget -qO- eth0.me)':'$(cat $HOME/.dora/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```

### Key management <a href="#key-management" id="key-management"></a>

Add New Wallet

```bash
dorad keys add $WALLET
```

Restore executing wallet

```bash
dorad keys add $WALLET --recover
```

List All Wallets

```bash
dorad keys list
```

Delete wallet

```bash
dorad keys delete $WALLET
```

Check Balance

```bash
dorad q bank balances $(dorad keys show $WALLET -a)
```

Export Key (save to wallet.backup)

```bash
dorad keys export $WALLET
```

Import Key (restore from wallet.backup)

```bash
dorad keys import $WALLET wallet.backup
```

### Tokens <a href="#tokens" id="tokens"></a>



Withdraw all rewards

```bash
dorad tx distribution withdraw-all-rewards --from $WALLET --chain-id vota-vk --fees 20000000000000000peaka
```

Withdraw rewards and commission from your validator

```bash
dorad tx distribution withdraw-rewards $VALOPER_ADDRESS --from $WALLET --commission --chain-id vota-vk --fees 20000000000000000peaka -y
```

Check your balance

```bash
dorad query bank balances $WALLET_ADDRESS
```

Delegate to Yourself

```bash
dorad tx staking delegate $(dorad keys show $WALLET --bech val -a) 1000000peaka --from $WALLET --chain-id vota-vk --fees 20000000000000000peaka -y
```

Delegate

```bash
dorad tx staking delegate <TO_VALOPER_ADDRESS> 1000000peaka --from $WALLET --chain-id vota-vk --fees 20000000000000000peaka -y
```

Redelegate Stake to Another Validator

```bash
dorad tx staking redelegate $VALOPER_ADDRESS <TO_VALOPER_ADDRESS> 1000000peaka --from $WALLET --chain-id vota-vk --fees 20000000000000000peaka -y
```

Unbond

```bash
dorad tx staking unbond $(dorad keys show $WALLET --bech val -a) 1000000peaka --from $WALLET --chain-id vota-vk --fees 20000000000000000peaka -y
```

Transfer Funds

```bash
dorad tx bank send $WALLET_ADDRESS <TO_WALLET_ADDRESS> 1000000peaka --fees 20000000000000000peaka -y
```

### Validator operations <a href="#validator-operations" id="validator-operations"></a>



Create New Validator

```bash
dorad tx staking create-validator \
--amount 1000000peaka \
--from $WALLET \
--commission-rate 0.1 \
--commission-max-rate 0.2 \
--commission-max-change-rate 0.01 \
--min-self-delegation 1 \
--pubkey $(dorad tendermint show-validator) \
--moniker "$MONIKER" \
--identity "" \
--details "xxxxxxxxxxxx" \
--chain-id vota-vk \
--fees 20000000000000000peaka \
-y
```

Edit Existing Validator

```bash
dorad tx staking edit-validator \
--commission-rate 0.1 \
--new-moniker "$MONIKER" \
--identity "" \
--details "xxxxxxxxxxxx" \
--from $WALLET \
--chain-id vota-vk \
--fees 20000000000000000peaka \
-y
```

Validator info

```bash
dorad status 2>&1 | jq .ValidatorInfo
```

Validator Details

```bash
dorad q staking validator $(dorad keys show $WALLET --bech val -a)
```

Jailing info

```bash
dorad q slashing signing-info $(dorad tendermint show-validator)
```

Slashing parameters

```bash
dorad q slashing params
```

Unjail validator

```bash
dorad tx slashing unjail --from $WALLET --chain-id vota-vk --fees 20000000000000000peaka -y
```

Active Validators List

```bash
dorad q staking validators -oj --limit=2000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " 	 " + .description.moniker' | sort -gr | nl
```

Check Validator key

```bash
[[ $(dorad q staking validator $VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(dorad status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "Your key status is ok" || echo -e "Your key status is error"
```

Signing info

```bash
dorad q slashing signing-info $(dorad tendermint show-validator)
```

### Governance <a href="#governance" id="governance"></a>



Create New Text Proposal

```bash
dorad  tx gov submit-proposal \
--title "" \
--description "" \
--deposit 1000000peaka \
--type Text \
--from $WALLET \
--fees 20000000000000000peaka \
-y 
```

Proposals List

```bash
dorad query gov proposals
```



View proposal

```bash
dorad query gov proposal 1
```

Vote

```bash
dorad tx gov vote 1 yes --from $WALLET --chain-id vota-vk  --fees 20000000000000000peaka -y
```
