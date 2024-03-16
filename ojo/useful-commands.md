# Useful commands

### <mark style="color:blue;">Service operations</mark> ⚙️ <a href="#service-operations" id="service-operations"></a>

Check logs

```bash
sudo journalctl -u ojod -f
```

Start service

```bash
sudo systemctl start ojod
```

Stop service

```bash
sudo systemctl stop ojod
```

Restart service

```bash
sudo systemctl restart ojod
```

Check service status

```bash
sudo systemctl status ojod
```

Reload services

```bash
sudo systemctl daemon-reload
```

Enable Service

```bash
sudo systemctl enable ojod
```

Disable Service

```bash
sudo systemctl disable ojod
```

Sync info

```bash
ojod status 2>&1 | jq .SyncInfo
```

Node info

```bash
ojod status 2>&1 | jq .NodeInfo
```

Your node peer

```bash
echo $(ojod tendermint show-node-id)'@'$(wget -qO- eth0.me)':'$(cat $HOME/.ojo/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```

### <mark style="color:purple;">Key management</mark> <a href="#key-management" id="key-management"></a>

Add New Wallet

```bash
ojod keys add $WALLET
```

Restore executing wallet

```bash
ojod keys add $WALLET --recover
```

List All Wallets

```bash
ojod keys list
```

Delete wallet

```bash
ojod keys delete $WALLET
```

Check Balance

```bash
ojod q bank balances $(ojod keys show $WALLET -a)
```

Export Key (save to wallet.backup)

```bash
ojod keys export $WALLET
```

Import Key (restore from wallet.backup)

```bash
ojod keys import $WALLET wallet.backup
```

### <mark style="color:orange;">Tokens</mark> <a href="#tokens" id="tokens"></a>

Withdraw all rewards

```bash
ojod tx distribution withdraw-all-rewards --from $WALLET --chain-id ojo-devnet --gas auto --gas-adjustment 1.5
```

Withdraw rewards and commission from your validator

```bash
ojod tx distribution withdraw-rewards $VALOPER_ADDRESS --from $WALLET --commission --chain-id ojo-devnet --gas auto --gas-adjustment 1.5 -y
```

Check your balance

```bash
ojod query bank balances $WALLET_ADDRESS
```

Delegate to Yourself

```bash
ojod tx staking delegate $(ojod keys show $WALLET --bech val -a) 1000000uojo --from $WALLET --chain-id ojo-devnet --gas auto --gas-adjustment 1.5 -y
```

Delegate

```bash
ojod tx staking delegate <TO_VALOPER_ADDRESS> 1000000uojo --from $WALLET --chain-id ojo-devnet --gas auto --gas-adjustment 1.5 -y
```

Redelegate Stake to Another Validator

```bash
ojod tx staking redelegate $VALOPER_ADDRESS <TO_VALOPER_ADDRESS> 1000000uojo --from $WALLET --chain-id ojo-devnet --gas auto --gas-adjustment 1.5 -y
```

Unbond

```bash
ojod tx staking unbond $(ojod keys show $WALLET --bech val -a) 1000000uojo --from $WALLET --chain-id ojo-devnet --gas auto --gas-adjustment 1.5 -y
```

Transfer Funds

```bash
ojod tx bank send $WALLET_ADDRESS <TO_WALLET_ADDRESS> 1000000uojo --gas auto --gas-adjustment 1.5 -y
```

### <mark style="color:red;">Validator operations</mark> <a href="#validator-operations" id="validator-operations"></a>

Create New Validator

```bash
ojod tx staking create-validator \
--amount 1000000uojo \
--from $WALLET \
--commission-rate 0.1 \
--commission-max-rate 0.2 \
--commission-max-change-rate 0.01 \
--min-self-delegation 1 \
--pubkey $(ojod tendermint show-validator) \
--moniker "$MONIKER" \
--identity "" \
--details "XXXXXXXXXX" \
--chain-id ojo-devnet \
--gas auto --gas-adjustment 1.5 \
-y
```

Edit Existing Validator

```bash
ojod tx staking edit-validator \
--commission-rate 0.1 \
--new-moniker "$MONIKER" \
--identity "" \
--details "XXXXXXXXXXXXX" \
--from $WALLET \
--chain-id ojo-devnet \
--gas auto --gas-adjustment 1.5 \
-y
```

Validator info

```bash
ojod status 2>&1 | jq .ValidatorInfo
```

Validator Details

```bash
ojod q staking validator $(ojod keys show $WALLET --bech val -a)
```

Jailing info

```bash
ojod q slashing signing-info $(ojod tendermint show-validator)
```

Unjail validator

```bash
ojod tx slashing unjail --from $WALLET --chain-id ojo-devnet --gas auto --gas-adjustment 1.5 -y
```

Active Validators List

```bash
ojod q staking validators -oj --limit=2000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " 	 " + .description.moniker' | sort -gr | nl
```

Check Validator key

```bash
[[ $(ojod q staking validator $VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(ojod status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "Your key status is ok" || echo -e "Your key status is error"
```

Signing info

```bash
ojod q slashing signing-info $(ojod tendermint show-validator)
```

### Governance <a href="#governance" id="governance"></a>

Create New Text Proposal

```bash
ojod  tx gov submit-proposal \
--title "" \
--description "" \
--deposit 1000000uojo \
--type Text \
--from $WALLET \
--gas auto --gas-adjustment 1.5 \
-y 
```

Proposals List

```bash
ojod query gov proposals
```

Proposal

View proposal

```bash
ojod query gov proposal 1
```

Vote

```bash
ojod tx gov vote 1 yes --from $WALLET --chain-id ojo-devnet  --gas auto --gas-adjustment 1.5 -y
```



#### Delete Node

```
sudo systemctl stop ojod
sudo systemctl disable ojod
sudo rm -rf /etc/systemd/system/ojod.service
sudo rm $(which ojod)
sudo rm -rf $HOME/.ojo
sed -i "/OJO_/d" $HOME/.bash_profile
```
