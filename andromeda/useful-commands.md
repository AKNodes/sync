# Useful commands

### <mark style="color:blue;">Service operations</mark> ⚙️ <a href="#service-operations" id="service-operations"></a>

Check logs

```bash
sudo journalctl -u andromedad -f
```

Start service

```bash
sudo systemctl start andromedad
```

Stop service

```bash
sudo systemctl stop andromedad
```

Restart service

```bash
sudo systemctl restart andromedad
```

Check service status

```bash
sudo systemctl status andromedad
```

Reload services

```bash
sudo systemctl daemon-reload
```

Enable Service

```bash
sudo systemctl enable andromedad
```

Disable Service

```bash
sudo systemctl disable andromedad
```

Sync info

```bash
andromedad status 2>&1 | jq .SyncInfo
```

Node info

```bash
andromedad status 2>&1 | jq .NodeInfo
```

Your node peer

```bash
echo $(andromedad tendermint show-node-id)'@'$(wget -qO- eth0.me)':'$(cat $HOME/.andromedad/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```

### <mark style="color:purple;">Key</mark>  <a href="#key-management" id="key-management"></a>

Add New Wallet

```bash
andromedad keys add $WALLET
```

Restore executing wallet

```bash
andromedad keys add $WALLET --recover
```

List All Wallets

```bash
andromedad keys list
```

Delete wallet

```bash
andromedad keys delete $WALLET
```

Check Balance

```bash
andromedad q bank balances $(andromedad keys show $WALLET -a)
```

Export Key (save to wallet.backup)

```bash
andromedad keys export $WALLET
```

Import Key (restore from wallet.backup)

```bash
andromedad keys import $WALLET wallet.backup
```

### <mark style="color:orange;">Tokens</mark> <a href="#tokens" id="tokens"></a>

Withdraw all rewards

```bash
andromedad tx distribution withdraw-all-rewards --from $WALLET --chain-id galileo-3 --gas auto --gas-adjustment 1.5
```

Withdraw rewards and commission from your validator

```bash
andromedad tx distribution withdraw-rewards $VALOPER_ADDRESS --from $WALLET --commission --chain-id galileo-3 --gas auto --gas-adjustment 1.5 -y
```

Check your balance

```bash
andromedad query bank balances $WALLET_ADDRESS
```

Delegate to Yourself

```bash
andromedad tx staking delegate $(andromedad keys show $WALLET --bech val -a) 1000000uandr --from $WALLET --chain-id galileo-3 --gas auto --gas-adjustment 1.5 -y
```

Delegate

```bash
andromedad tx staking delegate <TO_VALOPER_ADDRESS> 1000000uandr --from $WALLET --chain-id galileo-3 --gas auto --gas-adjustment 1.5 -y
```

Redelegate Stake to Another Validator

```bash
andromedad tx staking redelegate $VALOPER_ADDRESS <TO_VALOPER_ADDRESS> 1000000uandr --from $WALLET --chain-id galileo-3 --gas auto --gas-adjustment 1.5 -y
```

Unbond

```bash
andromedad tx staking unbond $(andromedad keys show $WALLET --bech val -a) 1000000uandr --from $WALLET --chain-id galileo-3 --gas auto --gas-adjustment 1.5 -y
```

Transfer Funds

```bash
andromedad tx bank send $WALLET_ADDRESS <TO_WALLET_ADDRESS> 1000000uandr --gas auto --gas-adjustment 1.5 -y
```

### <mark style="color:red;">Validator operations</mark> <a href="#validator-operations" id="validator-operations"></a>

Create New Validator

```bash
andromedad tx staking create-validator \
--amount 1000000uandr \
--from $WALLET \
--commission-rate 0.1 \
--commission-max-rate 0.2 \
--commission-max-change-rate 0.01 \
--min-self-delegation 1 \
--pubkey $(andromedad tendermint show-validator) \
--moniker "$MONIKER" \
--identity "" \
--details "xxxxxxxx" \
--chain-id galileo-3 \
--gas auto --gas-adjustment 1.5 \
-y
```

Edit Existing Validator

```bash
andromedad tx staking edit-validator \
--commission-rate 0.1 \
--new-moniker "$MONIKER" \
--identity "" \
--details "xxxxxxxxx" \
--from $WALLET \
--chain-id galileo-3 \
--gas auto --gas-adjustment 1.5 \
-y
```

Validator info

```bash
andromedad status 2>&1 | jq .ValidatorInfo
```

Validator Details

```bash
andromedad q staking validator $(andromedad keys show $WALLET --bech val -a)
```

Jailing info

```bash
andromedad q slashing signing-info $(andromedad tendermint show-validator)
```

Unjail validator

```bash
andromedad tx slashing unjail --from $WALLET --chain-id galileo-3 --gas auto --gas-adjustment 1.5 -y
```

Active Validators List

```bash
andromedad q staking validators -oj --limit=2000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " 	 " + .description.moniker' | sort -gr | nl
```

Check Validator key

```bash
[[ $(andromedad q staking validator $VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(andromedad status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "Your key status is ok" || echo -e "Your key status is error"
```

Signing info

```bash
andromedad q slashing signing-info $(andromedad tendermint show-validator)
```

### Governance <a href="#governance" id="governance"></a>

Create New Text Proposal

```bash
andromedad  tx gov submit-proposal \
--title "" \
--description "" \
--deposit 1000000uandr \
--type Text \
--from $WALLET \
--gas auto --gas-adjustment 1.5 \
-y 
```

Proposals List

```bash
andromedad query gov proposals
```

View proposal

```bash
andromedad query gov proposal 1
```

Vote

```bash
andromedad tx gov vote 1 yes --from $WALLET --chain-id galileo-3  --gas auto --gas-adjustment 1.5 -y
```



#### <mark style="color:yellow;">Delete Node</mark>

```
sudo systemctl stop andromedad
sudo systemctl disable andromedad
sudo rm -rf /etc/systemd/system/andromedad.service
sudo rm $(which andromedad)
sudo rm -rf $HOME/.andromedad
sed -i "/ANDROMEDA_/d" $HOME/.bash_profile
```
