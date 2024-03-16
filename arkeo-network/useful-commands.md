# Useful commands

### <mark style="color:yellow;">Service operations</mark> ⚙️ <a href="#service-operations" id="service-operations"></a>

Check logs

```bash
sudo journalctl -u arkeod -f
```

Start service

```bash
sudo systemctl start arkeod
```

Stop service

```bash
sudo systemctl stop arkeod
```

Restart service

```bash
sudo systemctl restart arkeod
```

Check service status

```bash
sudo systemctl status arkeod
```

Reload services

```bash
sudo systemctl daemon-reload
```

Enable Service

```bash
sudo systemctl enable arkeod
```

Disable Service

```bash
sudo systemctl disable arkeod
```

Sync info

```bash
arkeod status 2>&1 | jq .SyncInfo
```

Node info

```bash
arkeod status 2>&1 | jq .NodeInfo
```

Your node peer

```bash
echo $(arkeod tendermint show-node-id)'@'$(wget -qO- eth0.me)':'$(cat $HOME/.arkeo/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```

## _<mark style="color:purple;">Key</mark>_

#### Add New Key

```
arkeod keys add wallet
```

#### Recover Existing Key

```
arkeod keys add wallet --recover
```

#### List All Keys

```
arkeod keys list
```

#### Delete Key

```
arkeod keys delete wallet
```

#### Export Key (save to wallet.backup)

```
arkeod keys export wallet
```

#### Import Key

```
arkeod keys import wallet wallet.backup
```

#### Query Wallet Balance

```
arkeod q bank balances $(arkeod keys show wallet -a)
```

### <mark style="color:green;">Tokens</mark> <a href="#tokens" id="tokens"></a>

Withdraw all rewards

```bash
arkeod tx distribution withdraw-all-rewards --from $WALLET --chain-id arkeo --gas auto --gas-adjustment 1.5
```

Withdraw rewards and commission from your validator

```bash
arkeod tx distribution withdraw-rewards $VALOPER_ADDRESS --from $WALLET --commission --chain-id arkeo --gas auto --gas-adjustment 1.5 -y
```

Check your balance

```bash
arkeod query bank balances $WALLET_ADDRESS
```

Delegate to Yourself

```bash
arkeod tx staking delegate $(arkeod keys show $WALLET --bech val -a) 1000000uarkeo --from $WALLET --chain-id arkeo --gas auto --gas-adjustment 1.5 -y
```

Delegate

```bash
arkeod tx staking delegate <TO_VALOPER_ADDRESS> 1000000uarkeo --from $WALLET --chain-id arkeo --gas auto --gas-adjustment 1.5 -y
```

Redelegate Stake to Another Validator

```bash
arkeod tx staking redelegate $VALOPER_ADDRESS <TO_VALOPER_ADDRESS> 1000000uarkeo --from $WALLET --chain-id arkeo --gas auto --gas-adjustment 1.5 -y
```

Unbond

```bash
arkeod tx staking unbond $(arkeod keys show $WALLET --bech val -a) 1000000uarkeo --from $WALLET --chain-id arkeo --gas auto --gas-adjustment 1.5 -y
```

Transfer Funds

```bash
arkeod tx bank send $WALLET_ADDRESS <TO_WALLET_ADDRESS> 1000000uarkeo --gas auto --gas-adjustment 1.5 -y
```

### <mark style="color:red;">Validator operations</mark> <a href="#validator-operations" id="validator-operations"></a>

Create New Validator

```bash
arkeod tx staking create-validator \
--amount 1000000uarkeo \
--from $WALLET \
--commission-rate 0.1 \
--commission-max-rate 0.2 \
--commission-max-change-rate 0.01 \
--min-self-delegation 1 \
--pubkey $(arkeod tendermint show-validator) \
--moniker "$MONIKER" \
--identity "" \
--details "xxxxxxxxxxxx" \
--chain-id arkeo \
--gas auto --gas-adjustment 1.5 \
-y
```

Edit Existing Validator

```bash
arkeod tx staking edit-validator \
--commission-rate 0.1 \
--new-moniker "$MONIKER" \
--identity "" \
--details "xxxxxxxxxx" \
--from $WALLET \
--chain-id arkeo \
--gas auto --gas-adjustment 1.5 \
-y
```

Validator info

```bash
arkeod status 2>&1 | jq .ValidatorInfo
```

Validator Details

```bash
arkeod q staking validator $(arkeod keys show $WALLET --bech val -a)
```

Jailing info

```bash
arkeod q slashing signing-info $(arkeod tendermint show-validator)
```

Unjail validator

```bash
arkeod tx slashing unjail --from $WALLET --chain-id arkeo --gas auto --gas-adjustment 1.5 -y
```

Active Validators List

```bash
arkeod q staking validators -oj --limit=2000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " 	 " + .description.moniker' | sort -gr | nl
```

Check Validator key

```bash
[[ $(arkeod q staking validator $VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(arkeod status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "Your key status is ok" || echo -e "Your key status is error"
```

Signing info

```bash
arkeod q slashing signing-info $(arkeod tendermint show-validator)
```

### <mark style="color:blue;">Governance</mark> <a href="#governance" id="governance"></a>

Create New Text Proposal

```bash
arkeod  tx gov submit-proposal \
--title "" \
--description "" \
--deposit 1000000uarkeo \
--type Text \
--from $WALLET \
--gas auto --gas-adjustment 1.5 \
-y 
```

Proposals List

```bash
arkeod query gov proposals
```

Proposal IDProposal optionYesNoNo with vetoAbstain

View proposal

```bash
arkeod query gov proposal 1
```

Vote

```bash
arkeod tx gov vote 1 yes --from $WALLET --chain-id arkeo  --gas auto --gas-adjustment 1.5 -y
```



#### Delete Node

```
sudo systemctl stop arkeod
sudo systemctl disable arkeod
sudo rm -rf /etc/systemd/system/arkeod.service
sudo rm $(which arkeod)
sudo rm -rf $HOME/.arkeo
sed -i "/ARKEO_/d" $HOME/.bash_profile
```
