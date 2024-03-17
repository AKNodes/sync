# Useful commands

### <mark style="color:purple;">Keys Management</mark>

**Create wallet**

```
wardend keys add wallet
```

**Recover wallet**

```
wardend keys add wallet --recover
```

**Check all keys**

```
wardend keys list
```

**Check Balance**

```
wardend query bank balances xxxxxxx
```

**Delete Key**

```
wardend keys delete Wallet_Name
```

**Export Key**

```
wardend keys export wallet
```

**Import Key**

```
wardend keys import wallet wallet.backup
```

## <mark style="color:orange;">Validator Management</mark>



**Edit Validator**

```
wardend tx staking edit-validator \
--new-moniker "Your_Moniker" \
--identity "Keybase_ID" \
--details "Your_Description" \
--website "Your_Website" \
--security-contact "Your_Email" \
--chain-id alfama \
--commission-rate 0.05 \
--from Wallet_Name \
--gas 350000 -y
```

**Your Valoper-Address**

```
wardend keys show Wallet_Name --bech val
```

**Your Validator-Info**

```
wardend query staking validator VALOPER
```

**Jail Info**

```
wardend query slashing signing-info $(wardend tendermint show-validator)
```

**Unjail**

```
wardend tx slashing unjail --from Wallet_name --chain-id alfama --gas 350000 -y
```

**Active Validators List**

```
wardend q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

**Inactive Validators List**

```
wardend q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_UNBONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

**Check that your key matches the validator**&#x20;

```
VALOPER=Enter_Your_valoper_Here
[[ $(wardend  q staking validator $VALOPER -oj | jq -r .consensus_pubkey.key) = $(wardend status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\nYou win\n" || echo -e "\nYou lose\n"
```

**Withdraw all rewards from all validators**

```
wardend tx distribution withdraw-all-rewards --from Wallet_Name --chain-id alfama --gas 350000 -y
```

**Withdraw and commission from your Validator**

```
wardend tx distribution withdraw-rewards wardendvaloper1amxp0k0hg4edrxg85v07t9ka2tfuhamhldgf8e --from Wallet_Name --gas 350000 --chain-id=alfama --commission -y
```

**Delegate tokens to your validator**

```
wardend tx staking delegate VALOPER "100000000"uward --from Wallet_Name --gas 350000 --chain-id=alfama -y
```

**Delegate tokens to different validator**

```
wardend tx staking delegate VALOPER "100000000"uward --from Wallet_Name --gas 350000 --chain-id=alfama -y
```

**Redelegate tokens to another validator**

```
wardend tx staking redelegate VALOPER VALOPER "100000000"uward --from Wallet_Name --gas 350000  --chain-id=alfama -y
```

**Unbond tokens from your validator or different validator**

```
wardend tx staking unbond Your_wardenvalpoer........ "100000000"uward --from Wallet_Name --gas 350000 --chain-id=alfama -y
wardend tx staking unbond wardendvalpoer........ "100000000"uward --from Wallet_Name --gas 350000 --chain-id=alfama -y
```

**Transfer tokens from wallet to wallet**

```
wardend tx bank send Your_wardenaddress............ wardenaddress........... "1000000000000000000"uward --gas 350000 --chain-id=alfama -y
```

## 📝Governance

**View all proposals**

```
wardend query gov proposals
```

**View specific proposal**

```
wardend query gov proposal 1
```

**Vote yes**

```
wardend tx gov vote 1 yes --from Wallet_Name --gas 350000  --chain-id=alfama -y
```

**Vote no**

```
wardend tx gov vote 1 no --from Wallet_Name --gas 350000  --chain-id=alfama -y
```

**Vote abstain**

```
wardend tx gov vote 1 abstain --from Wallet_Name --gas 350000  --chain-id=alfama -y
```

**Vote no\_with\_veto**

```
wardend tx gov vote 1 no_with_veto --from Wallet_Name --gas 350000  --chain-id=alfama -y
```
