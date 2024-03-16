# Useful commands

## <mark style="color:blue;">Key management</mark>

#### Add new key

```bash
evmosd keys add wallet
```

#### Recover existing key

```bash
evmosd keys add wallet --recover
```

#### List all keys

```bash
evmosd keys list
```

#### Delete key

```bash
evmosd keys delete wallet
```

#### Query wallet balance

```bash
evmosd q bank balances $(evmosd keys show wallet -a)
```

## <mark style="color:purple;">Validator</mark>

#### Create new validator

```bash
evmosd tx staking create-validator \
--amount 1000000aevmos \
--pubkey $(evmosd tendermint show-validator) \
--moniker "YOUR_MONIKER_NAME" \
--identity "YOUR_KEYBASE_ID" \
--details "YOUR_DETAILS" \
--website "YOUR_WEBSITE_URL" \
--chain-id evmos_9001-2 \
--commission-rate 0.05 \
--commission-max-rate 0.20 \
--commission-max-change-rate 0.01 \
--min-self-delegation 1 \
--from wallet \
--gas-adjustment 1.4 \
--gas auto \
--gas-prices 25000000aevmos \
-y
```

#### Edit validator

```bash
evmosd tx staking edit-validator \
--new-moniker "YOUR_MONIKER_NAME" \
--identity "YOUR_KEYBASE_ID" \
--details "YOUR_DETAILS" \
--website "YOUR_WEBSITE_URL" \
--chain-id evmos_9001-2 \
--commission-rate 0.05 \
--from wallet \
--gas-adjustment 1.4 \
--gas auto \
--gas-prices 25000000aevmos \
-y
```

#### Unjail validator

```bash
evmosd tx slashing unjail --from wallet --chain-id evmos_9001-2 --gas-adjustment 1.4 --gas auto --gas-prices 25000000aevmos -y
```

#### Jail reason

```bash
evmosd query slashing signing-info $(evmosd tendermint show-validator)
```

#### List all active validators

```bash
evmosd q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

#### List all inactive validators

```bash
evmosd q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_UNBONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

#### Withdraw rewards from all validators

```bash
evmosd tx distribution withdraw-all-rewards --from wallet --chain-id evmos_9001-2 --gas-adjustment 1.4 --gas auto --gas-prices 25000000aevmos -y
```

#### Withdraw commission and rewards from your validator

```bash
evmosd tx distribution withdraw-rewards $(evmosd keys show wallet --bech val -a) --commission --from wallet --chain-id evmos_9001-2 --gas-adjustment 1.4 --gas auto --gas-prices 25000000aevmos -y
```

#### Delegate to yourself

```bash
evmosd tx staking delegate $(evmosd keys show wallet --bech val -a) 1000000aevmos --from wallet --chain-id evmos_9001-2 --gas-adjustment 1.4 --gas auto --gas-prices 25000000aevmos -y
```

#### Delegate tokens to validator

```bash
evmosd tx staking delegate <TO_VALOPER_ADDRESS> 1000000aevmos --from wallet --chain-id evmos_9001-2 --gas-adjustment 1.4 --gas auto --gas-prices 25000000aevmos -y
```

#### Redelegate tokens to another validator

```bash
evmosd tx staking redelegate $(evmosd keys show wallet --bech val -a) <TO_VALOPER_ADDRESS> 1000000aevmos --from wallet --chain-id evmos_9001-2 --gas-adjustment 1.4 --gas auto --gas-prices 25000000aevmos -y
```

#### Unbond tokens from your validator

```bash
evmosd tx staking unbond $(evmosd keys show wallet --bech val -a) 1000000aevmos --from wallet --chain-id evmos_9001-2 --gas-adjustment 1.4 --gas auto --gas-prices 25000000aevmos -y
```

#### Send tokens to the wallet

```bash
evmosd tx bank send wallet <TO_WALLET_ADDRESS> 1000000aevmos --from wallet --chain-id evmos_9001-2 --gas-adjustment 1.4 --gas auto --gas-prices 25000000aevmos -y
```

#### View validator details

```bash
evmosd q staking validator $(evmosd keys show wallet --bech val -a)
```

#### Slashing params

```bash
evmosd q slashing params
```

#### Signing Info

```bash
evmosd q slashing signing-info $(evmosd tendermint show-validator)
```

#### Get validator info

```bash
evmosd status 2>&1 | jq .ValidatorInfo
```

#### Get sync info

```bash
evmosd status 2>&1 | jq .SyncInfo
```

#### Check validator key

```bash
[[ $(evmosd q staking validator $(evmosd keys show wallet --bech val -a) -oj | jq -r .consensus_pubkey.key) = $(evmosd status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

#### Governance

```bash
evmosd tx gov vote 1 yes --from wallet --chain-id evmos_9001-2 --gas-prices 25000000aevmos --gas-adjustment 1.5 --gas auto -y
```

#### Remove node

```bash
cd $HOME
sudo systemctl stop evmosd
sudo systemctl disable evmosd
sudo rm /etc/systemd/system/evmosd.service
sudo systemctl daemon-reload
rm -f $(which evmosd)
rm -rf $HOME/.evmosd
rm -rf $HOME/evmos
```

#### Reload service configuration

```bash
sudo systemctl daemon-reload
```

#### Enable service

```bash
sudo systemctl enable evmosd
```

#### Disable service

```bash
sudo systemctl disable evmosd
```

#### Start service

```bash
sudo systemctl start evmosd
```

#### Stop service

```bash
sudo systemctl stop evmosd
```

#### Restart service

```bash
sudo systemctl restart evmosd
```

#### Check service status

```bash
sudo systemctl status evmosd
```

#### Check service logs

```bash
sudo journalctl -u evmosd -f --no-hostname -o cat
```
