# Useful commands

#### Add new key

```bash
axelard keys add wallet
```

#### Recover existing key

```bash
axelard keys add wallet --recover
```

#### List all keys

```bash
axelard keys list
```

#### Delete key

```bash
axelard keys delete wallet
```

#### Query wallet balance

```bash
axelard q bank balances $(axelard keys show wallet -a)
```

#### Create new validator

```bash
axelard tx staking create-validator \
--amount 1000000uaxl \
--pubkey $(axelard tendermint show-validator) \
--moniker "YOUR_MONIKER_NAME" \
--identity "YOUR_KEYBASE_ID" \
--details "YOUR_DETAILS" \
--website "YOUR_WEBSITE_URL" \
--chain-id axelar-dojo-1 \
--commission-rate 0.05 \
--commission-max-rate 0.20 \
--commission-max-change-rate 0.01 \
--min-self-delegation 1 \
--from wallet \
--gas-adjustment 1.4 \
--gas auto \
--gas-prices 0.007uaxl \
-y
```

#### Edit validator

```bash
axelard tx staking edit-validator \
--new-moniker "YOUR_MONIKER_NAME" \
--identity "YOUR_KEYBASE_ID" \
--details "YOUR_DETAILS" \
--website "YOUR_WEBSITE_URL" \
--chain-id axelar-dojo-1 \
--commission-rate 0.05 \
--from wallet \
--gas-adjustment 1.4 \
--gas auto \
--gas-prices 0.007uaxl \
-y
```

#### Unjail validator

```bash
axelard tx slashing unjail --from wallet --chain-id axelar-dojo-1 --gas-adjustment 1.4 --gas auto --gas-prices 0.007uaxl -y
```

#### Jail reason

```bash
axelard query slashing signing-info $(axelard tendermint show-validator)
```

#### List all active validators

```bash
axelard q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

#### List all inactive validators

```bash
axelard q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_UNBONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

#### Withdraw rewards from all validators

```bash
axelard tx distribution withdraw-all-rewards --from wallet --chain-id axelar-dojo-1 --gas-adjustment 1.4 --gas auto --gas-prices 0.007uaxl -y
```

#### Withdraw commission and rewards from your validator

```bash
axelard tx distribution withdraw-rewards $(axelard keys show wallet --bech val -a) --commission --from wallet --chain-id axelar-dojo-1 --gas-adjustment 1.4 --gas auto --gas-prices 0.007uaxl -y
```

#### Delegate to yourself

```bash
axelard tx staking delegate $(axelard keys show wallet --bech val -a) 1000000uaxl --from wallet --chain-id axelar-dojo-1 --gas-adjustment 1.4 --gas auto --gas-prices 0.007uaxl -y
```

#### Delegate tokens to validator

```bash
axelard tx staking delegate <TO_VALOPER_ADDRESS> 1000000uaxl --from wallet --chain-id axelar-dojo-1 --gas-adjustment 1.4 --gas auto --gas-prices 0.007uaxl -y
```

#### Redelegate tokens to another validator

```bash
axelard tx staking redelegate $(axelard keys show wallet --bech val -a) <TO_VALOPER_ADDRESS> 1000000uaxl --from wallet --chain-id axelar-dojo-1 --gas-adjustment 1.4 --gas auto --gas-prices 0.007uaxl -y
```

#### Unbond tokens from your validator

```bash
axelard tx staking unbond $(axelard keys show wallet --bech val -a) 1000000uaxl --from wallet --chain-id axelar-dojo-1 --gas-adjustment 1.4 --gas auto --gas-prices 0.007uaxl -y
```

#### Send tokens to the wallet

```bash
axelard tx bank send wallet <TO_WALLET_ADDRESS> 1000000uaxl --from wallet --chain-id axelar-dojo-1 --gas-adjustment 1.4 --gas auto --gas-prices 0.007uaxl -y
```

#### View validator details

```bash
axelard q staking validator $(axelard keys show wallet --bech val -a)
```

#### Slashing params

```bash
axelard q slashing params
```

#### Signing Info

```bash
axelard q slashing signing-info $(axelard tendermint show-validator)
```

#### Get validator info

```bash
axelard status 2>&1 | jq .ValidatorInfo
```

#### Get sync info

```bash
axelard status --node http://localhost:17357 2>&1 | jq .SyncInfo
```

#### Check validator key

```bash
[[ $(axelard q staking validator $(axelard keys show wallet --bech val -a) -oj | jq -r .consensus_pubkey.key) = $(axelard status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

#### Governance

```bash
axelard tx gov vote 1 yes --from wallet --chain-id axelar-dojo-1 --gas-prices 0.007uaxl --gas-adjustment 1.5 --gas auto -y
```

#### Remove node

```bash
cd $HOME
sudo systemctl stop axelard
sudo systemctl disable axelard
sudo rm /etc/systemd/system/axelard.service
sudo systemctl daemon-reload
rm -f $(which axelard)
rm -rf $HOME/.axelar
rm -rf $HOME/axelar-core
```

#### Reload service configuration

```bash
sudo systemctl daemon-reload
```

#### Enable service

```bash
sudo systemctl enable axelard
```

#### Disable service

```bash
sudo systemctl disable axelard
```

#### Start service

```bash
sudo systemctl start axelard
```

#### Stop service

```bash
sudo systemctl stop axelard
```

#### Restart service

```bash
sudo systemctl restart axelard
```

#### Check service status

```bash
sudo systemctl status axelard
```

#### Check service logs

```bash
sudo journalctl -u axelard -f --no-hostname -o cat
```
