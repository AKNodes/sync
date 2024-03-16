# Useful commands

#### Keys

#### Add new key

```bash
nibid keys add wallet
```

#### Recover existing key

```bash
nibid keys add wallet --recover
```

#### List all keys

```bash
nibid keys list
```

#### Delete key

```bash
nibid keys delete wallet
```

#### Query wallet balance

```bash
nibid q bank balances $(nibid keys show wallet -a)
```

#### Create new validator

```bash
nibid tx staking create-validator \
--amount 10000000unibi \
--pubkey $(nibid tendermint show-validator) \
--moniker "YOUR_MONIKER_NAME" \
--identity "YOUR_KEYBASE_ID" \
--details "YOUR_DETAILS" \
--website "YOUR_WEBSITE_URL" \
--chain-id cataclysm-1 \
--commission-rate 0.1 \
--commission-max-rate 0.20 \
--commission-max-change-rate 0.01 \
--min-self-delegation 1 \
--from wallet \
--fees 5000unibi \
-y
```

#### Edit validator

```bash
nibid tx staking edit-validator \
--new-moniker "YOUR_MONIKER_NAME" \
--identity "YOUR_KEYBASE_ID" \
--details "YOUR_DETAILS" \
--website "YOUR_WEBSITE_URL" \
--chain-id cataclysm-1 \
--commission-rate 0.05 \
--from wallet \
--fees 5000unibi \
-y
```

#### Unjail validator

```bash
nibid tx slashing unjail --from wallet --chain-id cataclysm-1 --gas-adjustment 1.4 --gas auto --gas-prices 0.025unibi -y
```

#### Jail reason

```bash
nibid query slashing signing-info $(nibid tendermint show-validator)
```

#### List all active validators

```bash
nibid q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

#### List all inactive validators

```bash
nibid q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_UNBONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

#### Withdraw rewards from all validators

```bash
nibid tx distribution withdraw-all-rewards --from wallet --chain-id cataclysm-1 --gas-adjustment 1.4 --gas auto --gas-prices 0.025unibi -y
```

#### Withdraw commission and rewards from your validator

```bash
nibid tx distribution withdraw-rewards $(nibid keys show wallet --bech val -a) --commission --from wallet --chain-id cataclysm-1 --gas-adjustment 1.4 --gas auto --gas-prices 0.025unibi -y
```

#### Delegate to yourself

```bash
nibid tx staking delegate $(nibid keys show wallet --bech val -a) 1000000unibi --from wallet --chain-id cataclysm-1 --gas-adjustment 1.4 --gas auto --gas-prices 0.025unibi -y
```

#### Delegate tokens to validator

```bash
nibid tx staking delegate <TO_VALOPER_ADDRESS> 1000000unibi --from wallet --chain-id cataclysm-1 --gas-adjustment 1.4 --gas auto --gas-prices 0.025unibi -y
```

#### Redelegate tokens to another validator

```bash
nibid tx staking redelegate $(nibid keys show wallet --bech val -a) <TO_VALOPER_ADDRESS> 1000000unibi --from wallet --chain-id cataclysm-1 --gas-adjustment 1.4 --gas auto --gas-prices 0.025unibi -y
```

#### Unbond tokens from your validator

```bash
nibid tx staking unbond $(nibid keys show wallet --bech val -a) 1000000unibi --from wallet --chain-id cataclysm-1 --gas-adjustment 1.4 --gas auto --gas-prices 0.025unibi -y
```

#### Send tokens to the wallet

```bash
nibid tx bank send wallet <TO_WALLET_ADDRESS> 1000000unibi --from wallet --chain-id cataclysm-1 --gas-adjustment 1.4 --gas auto --gas-prices 0.025unibi -y
```

#### View validator details

```bash
nibid q staking validator $(nibid keys show wallet --bech val -a)
```

#### Slashing params

```bash
nibid q slashing params
```

#### Signing Info

```bash
nibid q slashing signing-info $(nibid tendermint show-validator)
```

#### Get validator info

```bash
nibid status 2>&1 | jq .ValidatorInfo
```

#### Get sync info

```bash
nibid status 2>&1 | jq .SyncInfo
```

#### Check validator key

```bash
[[ $(nibid q staking validator $(nibid keys show wallet --bech val -a) -oj | jq -r .consensus_pubkey.key) = $(nibid status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

#### Remove node

```bash
cd $HOME
sudo systemctl stop nibid
sudo systemctl disable nibid
sudo rm /etc/systemd/system/nibid.service
sudo systemctl daemon-reload
rm -f $(which nibid)
rm -rf $HOME/.nibid
rm -rf $HOME/nibiru
```

#### Reload service configuration

```bash
sudo systemctl daemon-reload
```

#### Enable service

```bash
sudo systemctl enable nibid
```

#### Disable service

```bash
sudo systemctl disable nibid
```

#### Start service

```bash
sudo systemctl start nibid
```

#### Stop service

```bash
sudo systemctl stop nibid
```

#### Restart service

```bash
sudo systemctl restart nibid
```

#### Check service status

```bash
sudo systemctl status nibid
```

#### Check service logs

```bash
sudo journalctl -u nibid -f --no-hostname -o cat
```



#### <mark style="color:purple;">Delete Node</mark>

```
sudo systemctl stop nibid
sudo systemctl disable nibid
sudo rm -rf /etc/systemd/system/nibid.service
sudo rm $(which nibid)
sudo rm -rf $HOME/.nibid
sed -i "/NIBIRU_/d" $HOME/.bash_profile
```
