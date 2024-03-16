# Useful commands

### Key Management <a href="#key" id="key"></a>

Add new key

```bash
swisstronikd keys add wallet
```

Recover existing key

```bash
swisstronikd keys add wallet --recover
```

List All key

```bash
swisstronikd keys list
```

Delete key

```bash
swisstronikd keys delete wallet
```

Export Key (save to wallet.backup)

```bash
swisstronikd keys export wallet
```

Import key

```bash
swisstronikd keys import wallet wallet.backup
```

Query Wallet Balance

```bash
swisstronikd q bank balances $(swisstronikd keys show wallet -a)
```

### Validator Management <a href="#validator" id="validator"></a>

Create Validator

```bash
swisstronikd tx staking create-validator \
  --amount "1000000uswrt" \
  --pubkey $(swisstronikd tendermint show-validator) \
  --moniker "<MONIKER>" \
  --identity "" \
  --website "YOUR WEBSITE" \
  --chain-id swisstronik_1291-1 \
  --commission-rate "0.01" \
  --commission-max-rate "0.2" \
  --commission-max-change-rate "0.01" \
  --min-self-delegation "1" \
  --gas-prices 7uswrt \
  --gas "auto" \
  --gas-adjustment "1.5" \
  --from wallet \
  -y
```

Edit Validator

```bash
swisstronikd tx staking edit-validator \
--new-moniker "<MONIKER>" \
--identity "" \
--details "Indonode Guide" \
--website "YOUR WEBSITE" \
--chain-id swisstronik_1291-1 \
--commission-rate "0.01" \
--gas-prices 7uswrt \
--gas "auto" \
--gas-adjustment "1.5" \
--from wallet \
-y
```

Unjail Validator

```bash
swisstronikd tx slashing unjail \
--chain-id swisstronik_1291-1 \
--gas-prices 7uswrt \
--gas-adjustment 1.5\
--gas "auto" \
--from wallet \
-y 
```

Signing Info

```bash
swisstronikd query slashing signing-info $(swisstronikd tendermint show-validator) 
```

List all active validators

```bash
swisstronikd q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " 	 " + .description.moniker' | sort -gr | nl 
```

List all inactive validators

```bash
swisstronikd q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_UNBONDED") or .status=="BOND_STATUS_UNBONDING")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " 	 " + .description.moniker' | sort -gr | nl 
```

View validators details

```bash
swisstronikd q staking validator $(swisstronikd keys show wallet --bech val -a) 
```

### Token Management <a href="#token" id="token"></a>

To valoper addressTo wallet addressAmount, uswrt

Withdraw rewards from all validators

```bash
swisstronikd tx distribution withdraw-all-rewards --from wallet --chain-id swisstronik_1291-1 --gas-prices 7uswrt  --gas-adjustment 1.5 --gas "auto" -y 
```

Withdraw comission and rewards from your validator

```bash
swisstronikd tx distribution withdraw-rewards $(swisstronikd keys show wallet --bech val -a) --commission --from wallet --chain-id swisstronik_1291-1 --gas-prices 7uswrt  --gas-adjustment 1.5 --gas "auto" -y 
```

Delegate to your validator

```bash
swisstronikd tx staking delegate $(swisstronikd keys show wallet --bech val -a) 1000000uswrt --from wallet --chain-id swisstronik_1291-1 --gas-prices 7uswrt  --gas-adjustment 1.5 --gas "auto" -y 
```

Delegate to other

```bash
c4ed tx staking delegate <TO_VALOPER_ADDRESS> 1000000uswrt --from wallet --chain-id swisstronik_1291-1 --gas-prices 7uswrt  --gas-adjustment 1.5 --gas "auto" -y 
```

Redelegate your stake to other validators

```bash
swisstronikd tx staking redelegate $(swisstronikd keys show wallet --bech val -a) <TO_VALOPER_ADDRESS> 1000000uswrt --from wallet --chain-id swisstronik_1291-1 --gas-prices 7uswrt  --gas-adjustment 1.5 --gas "auto" -y 
```

Unbond stake

```bash
swisstronikd tx staking unbond $(swisstronikd keys show wallet --bech val -a) 1000000uswrt --from wallet --chain-id swisstronik_1291-1 --gas-prices 7uswrt  --gas-adjustment 1.5 --gas "auto" -y 
```

Send tokens

```bash
swisstronikd tx bank send wallet <TO_WALLET_ADDRESS> 1000000uswrt --from wallet --chain-id swisstronik_1291-1 --gas-prices 7uswrt  --gas-adjustment 1.5 --gas "auto" -y 
```

### Governance <a href="#governance" id="governance"></a>

Create new text proposal

```bash
swisstronikd tx gov submit-proposal \
--title "" \
--description "" \
--deposit "1000000uswrt" \
--type "Text" \
--from wallet \
--gas-prices 7uswrt \ 
--gas-adjustment 1.5 \
--gas "auto" \
-y 
```

List all proposals

```bash
swisstronikd query gov proposals
```

Proposal IDProposal optionYesNoNo with vetoAbstain

Vote

```bash
swisstronikd tx gov vote 1 yes \
--from wallet \
--chain-id swisstronik_1291-1 \
--gas-prices 7uswrt \
--gas-adjustment 1.5 \
--gas "auto" \
-y 
```

### Utility <a href="#utility" id="utility"></a>

Set IndexerNULLKV

```bash
sed -i 's|^indexer *=.*|indexer = "null"|' $HOME/.swisstronik/config/config.toml
```

Get Validator info

```bash
swisstronikd status 2>&1 | jq .ValidatorInfo
```

Get denom info

```bash
swisstronikd q bank denom-metadata -oj | jq
```

Get sync status

```bash
swisstronikd status 2>&1 | jq .SyncInfo.catching_up
```

Get latest height

```bash
swisstronikd status 2>&1 | jq .SyncInfo.latest_block_height
```

Reset Node

```bash
swisstronikd tendermint unsafe-reset-all --home $HOME/.swisstronik --keep-addr-book
```

Delete Node

```bash
cd $HOME && sudo systemctl stop swisstronikd && sudo systemctl disable swisstronikd && sudo rm /etc/systemd/system/swisstronikd.service && sudo systemctl daemon-reload && sudo rm -rf $(which swisstronikd) && sudo rm -rf $HOME/.swisstronik && sudo rm -rf $(which swisstronikd) 
```

### Services Management <a href="#services" id="services"></a>

Reload Service

```bash
sudo systemctl daemon-reload
```

Enable Service

```bash
sudo systemctl enable swisstronikd
```

Disable Service

```bash
sudo systemctl disable swisstronikd
```

Start Service

```bash
sudo systemctl start swisstronikd
```

Stop Service

```bash
sudo systemctl stop swisstronikd
```

Restart Service

```bash
sudo systemctl restart swisstronikd
```

Check Service Status

```bash
sudo systemctl status swisstronikd
```

Check Service Logs

```bash
sudo journalctl -u swisstronikd -f --no-hostname -o cat
```
