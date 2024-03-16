# Useful commands

### <mark style="color:blue;">Service operations</mark> ⚙️ <a href="#service-operations" id="service-operations"></a>

Check logs

```bash
sudo journalctl -u haqqd -f
```

Start service

```bash
sudo systemctl start haqqd
```

Stop service

```bash
sudo systemctl stop haqqd
```

Restart service

```bash
sudo systemctl restart haqqd
```

Check service status

```bash
sudo systemctl status haqqd
```

Reload services

```bash
sudo systemctl daemon-reload
```

Enable Service

```bash
sudo systemctl enable haqqd
```

Disable Service

```bash
sudo systemctl disable haqqd
```

Sync info

```bash
haqqd status 2>&1 | jq .SyncInfo
```

Node info

```bash
haqqd status 2>&1 | jq .NodeInfo
```

Your node peer

```bash
echo $(haqqd tendermint show-node-id)'@'$(wget -qO- eth0.me)':'$(cat $HOME/.haqqd/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```

### <mark style="color:purple;">Key management</mark> <a href="#key-management" id="key-management"></a>

Add New Wallet

```bash
haqqd keys add $WALLET
```

Restore executing wallet

```bash
haqqd keys add $WALLET --recover
```

List All Wallets

```bash
haqqd keys list
```

Delete wallet

```bash
haqqd keys delete $WALLET
```

Check Balance

```bash
haqqd q bank balances $(haqqd keys show $WALLET -a)
```

Export Key (save to wallet.backup)

```bash
haqqd keys export $WALLET
```

Import Key (restore from wallet.backup)

```bash
haqqd keys import $WALLET wallet.backup
```

### <mark style="color:orange;">Tokens</mark> <a href="#tokens" id="tokens"></a>

Withdraw all rewards

```bash
haqqd tx distribution withdraw-all-rewards --from $WALLET --chain-id haqq_11235-1 --gas-adjustment=1.15 --gas auto --gas-prices "27500000000aISLM"
```

Withdraw rewards and commission from your validator

```bash
haqqd tx distribution withdraw-rewards $VALOPER_ADDRESS --from $WALLET --commission --chain-id haqq_11235-1 --gas-adjustment=1.15 --gas auto --gas-prices "27500000000aISLM" -y
```

Check your balance

```bash
haqqd query bank balances $WALLET_ADDRESS
```

Delegate to Yourself

```bash
haqqd tx staking delegate $(haqqd keys show $WALLET --bech val -a) 1000000aISLM --from $WALLET --chain-id haqq_11235-1 --gas-adjustment=1.15 --gas auto --gas-prices "27500000000aISLM" -y
```

Delegate

```bash
haqqd tx staking delegate <TO_VALOPER_ADDRESS> 1000000aISLM --from $WALLET --chain-id haqq_11235-1 --gas-adjustment=1.15 --gas auto --gas-prices "27500000000aISLM" -y
```

Redelegate Stake to Another Validator

```bash
haqqd tx staking redelegate $VALOPER_ADDRESS <TO_VALOPER_ADDRESS> 1000000aISLM --from $WALLET --chain-id haqq_11235-1 --gas-adjustment=1.15 --gas auto --gas-prices "27500000000aISLM" -y
```

Unbond

```bash
haqqd tx staking unbond $(haqqd keys show $WALLET --bech val -a) 1000000aISLM --from $WALLET --chain-id haqq_11235-1 --gas-adjustment=1.15 --gas auto --gas-prices "27500000000aISLM" -y
```

Transfer Funds

```bash
haqqd tx bank send $WALLET_ADDRESS <TO_WALLET_ADDRESS> 1000000aISLM --gas-adjustment=1.15 --gas auto --gas-prices "27500000000aISLM" -y
```

### <mark style="color:yellow;">Validator operations</mark> <a href="#validator-operations" id="validator-operations"></a>

Create New Validator

```bash
haqqd tx staking create-validator \
--amount 1000000aISLM \
--from $WALLET \
--commission-rate 0.1 \
--commission-max-rate 0.2 \
--commission-max-change-rate 0.01 \
--min-self-delegation 1 \
--pubkey $(haqqd tendermint show-validator) \
--moniker "$MONIKER" \
--identity "" \
--details "xxxxxxxxxxxx" \
--chain-id haqq_11235-1 \
--gas-adjustment=1.15 --gas auto --gas-prices "27500000000aISLM" \
-y
```

Edit Existing Validator

```bash
haqqd tx staking edit-validator \
--commission-rate 0.1 \
--new-moniker "$MONIKER" \
--identity "" \
--details "xxxxxxxx" \
--from $WALLET \
--chain-id haqq_11235-1 \
--gas-adjustment=1.15 --gas auto --gas-prices "27500000000aISLM" \
-y
```

Validator info

```bash
haqqd status 2>&1 | jq .ValidatorInfo
```

Validator Details

```bash
haqqd q staking validator $(haqqd keys show $WALLET --bech val -a)
```

Jailing info

```bash
haqqd q slashing signing-info $(haqqd tendermint show-validator)
```

Unjail validator

```bash
haqqd tx slashing unjail --broadcast-mode block --from $WALLET --chain-id haqq_11235-1 --gas-adjustment=1.15 --gas auto --gas-prices "27500000000aISLM" -y
```

Active Validators List

```bash
haqqd q staking validators -oj --limit=2000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " 	 " + .description.moniker' | sort -gr | nl
```

Check Validator key

```bash
[[ $(haqqd q staking validator $VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(haqqd status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "Your key status is ok" || echo -e "Your key status is error"
```

Signing info

```bash
haqqd q slashing signing-info $(haqqd tendermint show-validator)
```

### <mark style="color:yellow;">Governance</mark> <a href="#governance" id="governance"></a>

Create New Text Proposal

```bash
haqqd  tx gov submit-proposal \
--title "" \
--description "" \
--deposit 1000000aISLM \
--type Text \
--from $WALLET \
--gas-adjustment=1.15 --gas auto --gas-prices "27500000000aISLM" \
-y 
```

Proposals List

```bash
haqqd query gov proposals
```

View proposal

```bash
haqqd query gov proposal 1
```

Vote

```bash
haqqd tx gov vote 1 yes --from $WALLET --chain-id haqq_11235-1  --gas-adjustment=1.15 --gas auto --gas-prices "27500000000aISLM" -y
```

### <mark style="color:green;">Delete node</mark> <a href="#delete" id="delete"></a>

```bash
sudo systemctl stop haqqd
sudo systemctl disable haqqd
sudo rm -rf /etc/systemd/system/haqqd.service
sudo rm $(which haqqd)
sudo rm -rf $HOME/.haqqd
sed -i "/HAQQ_/d" $HOME/.bash_profile
```
