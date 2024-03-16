# Useful commands

### <mark style="color:blue;">Service operations</mark> ⚙️ <a href="#service-operations" id="service-operations"></a>

Check logs

```bash
sudo journalctl -u terpd -f
```

Start service

```bash
sudo systemctl start terpd
```

Stop service

```bash
sudo systemctl stop terpd
```

Restart service

```bash
sudo systemctl restart terpd
```

Check service status

```bash
sudo systemctl status terpd
```

Reload services

```bash
sudo systemctl daemon-reload
```

Enable Service

```bash
sudo systemctl enable terpd
```

Disable Service

```bash
sudo systemctl disable terpd
```

Sync info

```bash
terpd status 2>&1 | jq .SyncInfo
```

Node info

```bash
terpd status 2>&1 | jq .NodeInfo
```

Your node peer

```bash
echo $(terpd tendermint show-node-id)'@'$(wget -qO- eth0.me)':'$(cat $HOME/.terp/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```

### <mark style="color:purple;">Key management</mark> <a href="#key-management" id="key-management"></a>

Add New Wallet

```bash
terpd keys add $WALLET
```

Restore executing wallet

```bash
terpd keys add $WALLET --recover
```

List All Wallets

```bash
terpd keys list
```

Delete wallet

```bash
terpd keys delete $WALLET
```

Check Balance

```bash
terpd q bank balances $(terpd keys show $WALLET -a)
```

Export Key (save to wallet.backup)

```bash
terpd keys export $WALLET
```

Import Key (restore from wallet.backup)

```bash
terpd keys import $WALLET wallet.backup
```

### <mark style="color:orange;">Tokens</mark> <a href="#tokens" id="tokens"></a>

Withdraw all rewards

```bash
terpd tx distribution withdraw-all-rewards --from $WALLET --chain-id 90u-2 --gas auto --gas-adjustment 1.5
```

Withdraw rewards and commission from your validator

```bash
terpd tx distribution withdraw-rewards $VALOPER_ADDRESS --from $WALLET --commission --chain-id 90u-2 --gas auto --gas-adjustment 1.5 -y
```

Check your balance

```bash
terpd query bank balances $WALLET_ADDRESS
```

Delegate to Yourself

```bash
terpd tx staking delegate $(terpd keys show $WALLET --bech val -a) 1000000uterpx --from $WALLET --chain-id 90u-2 --gas auto --gas-adjustment 1.5 -y
```

Delegate

```bash
terpd tx staking delegate <TO_VALOPER_ADDRESS> 1000000uterpx --from $WALLET --chain-id 90u-2 --gas auto --gas-adjustment 1.5 -y
```

Redelegate Stake to Another Validator

```bash
terpd tx staking redelegate $VALOPER_ADDRESS <TO_VALOPER_ADDRESS> 1000000uterpx --from $WALLET --chain-id 90u-2 --gas auto --gas-adjustment 1.5 -y
```

Unbond

```bash
terpd tx staking unbond $(terpd keys show $WALLET --bech val -a) 1000000uterpx --from $WALLET --chain-id 90u-2 --gas auto --gas-adjustment 1.5 -y
```

Transfer Funds

```bash
terpd tx bank send $WALLET_ADDRESS <TO_WALLET_ADDRESS> 1000000uterpx --gas auto --gas-adjustment 1.5 -y
```

### <mark style="color:red;">Validator operations</mark> <a href="#validator-operations" id="validator-operations"></a>

Create New Validator

```bash
terpd tx staking create-validator \
--amount 1000000uterpx \
--from $WALLET \
--commission-rate 0.1 \
--commission-max-rate 0.2 \
--commission-max-change-rate 0.01 \
--min-self-delegation 1 \
--pubkey $(terpd tendermint show-validator) \
--moniker "$MONIKER" \
--identity "" \
--details "xxxxxxxxxxxxx" \
--chain-id 90u-2 \
--gas auto --gas-adjustment 1.5 \
-y
```

Edit Existing Validator

```bash
terpd tx staking edit-validator \
--commission-rate 0.1 \
--new-moniker "$MONIKER" \
--identity "" \
--details "xxxxxxxxxx" \
--from $WALLET \
--chain-id 90u-2 \
--gas auto --gas-adjustment 1.5 \
-y
```

Validator info

```bash
terpd status 2>&1 | jq .ValidatorInfo
```

Validator Details

```bash
terpd q staking validator $(terpd keys show $WALLET --bech val -a)
```

Jailing info

```bash
terpd q slashing signing-info $(terpd tendermint show-validator)
```

Unjail validator

```bash
terpd tx slashing unjail --from $WALLET --chain-id 90u-2 --gas auto --gas-adjustment 1.5 -y
```

Active Validators List

```bash
terpd q staking validators -oj --limit=2000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " 	 " + .description.moniker' | sort -gr | nl
```

Check Validator key

```bash
[[ $(terpd q staking validator $VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(terpd status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "Your key status is ok" || echo -e "Your key status is error"
```

Signing info

```bash
terpd q slashing signing-info $(terpd tendermint show-validator)
```

### <mark style="color:yellow;">Governance</mark> <a href="#governance" id="governance"></a>

Create New Text Proposal

```bash
terpd  tx gov submit-proposal \
--title "" \
--description "" \
--deposit 1000000uterpx \
--type Text \
--from $WALLET \
--gas auto --gas-adjustment 1.5 \
-y 
```

Proposals List

```bash
terpd query gov proposals
```

Proposal IDProposal optionYesNoNo with vetoAbstain

View proposal

```bash
terpd query gov proposal 1
```

Vote

```bash
terpd tx gov vote 1 yes --from $WALLET --chain-id 90u-2  --gas auto --gas-adjustment 1.5 -y
```



#### <mark style="background-color:green;">Delete Node</mark>

```
sudo systemctl stop terpd
sudo systemctl disable terpd
sudo rm -rf /etc/systemd/system/terpd.service
sudo rm $(which terpd)
sudo rm -rf $HOME/.terp
sed -i "/TERP_/d" $HOME/.bash_profile
```
