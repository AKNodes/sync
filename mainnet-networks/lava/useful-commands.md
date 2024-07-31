# Useful commands

### <mark style="color:blue;">Service operations</mark> ⚙️ <a href="#service-operations" id="service-operations"></a>

Check logs

```bash
sudo journalctl -u lavad -f
```

Start service

```bash
sudo systemctl start lavad
```

Stop service

```bash
sudo systemctl stop lavad
```

Restart service

```bash
sudo systemctl restart lavad
```

Check service status

```bash
sudo systemctl status lavad
```

Reload services

```bash
sudo systemctl daemon-reload
```

Enable Service

```bash
sudo systemctl enable lavad
```

Disable Service

```bash
sudo systemctl disable lavad
```

Sync info

```bash
lavad status 2>&1 | jq .SyncInfo
```

Node info

```bash
lavad status 2>&1 | jq .NodeInfo
```

Your node peer

```bash
echo $(lavad tendermint show-node-id)'@'$(wget -qO- eth0.me)':'$(cat $HOME/.lava/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```

### <mark style="color:purple;">Key management</mark> <a href="#key-management" id="key-management"></a>

Add New Wallet

```bash
lavad keys add $WALLET
```

Restore executing wallet

```bash
lavad keys add $WALLET --recover
```

List All Wallets

```bash
lavad keys list
```

Delete wallet

```bash
lavad keys delete $WALLET
```

Check Balance

```bash
lavad q bank balances $(lavad keys show $WALLET -a)
```

Export Key (save to wallet.backup)

```bash
lavad keys export $WALLET
```

Import Key (restore from wallet.backup)

```bash
lavad keys import $WALLET wallet.backup
```

### <mark style="color:orange;">Tokens</mark> <a href="#tokens" id="tokens"></a>

Withdraw all rewards

```bash
lavad tx distribution withdraw-all-rewards --from $WALLET --chain-id lava-mainnet-1 --gas=350000 --fees=1ulava
```

Withdraw rewards and commission from your validator

```bash
lavad tx distribution withdraw-rewards $VALOPER_ADDRESS --from $WALLET --commission --chain-id lava-mainnet-1 --gas=350000 --fees=1ulava
```

Check your balance

```bash
lavad query bank balances $WALLET_ADDRESS
```

Delegate to Yourself

```bash
lavad tx staking delegate $(lavad keys show $WALLET --bech val -a) 1000000ulava --from $WALLET --chain-id lava-mainnet-1 --gas=350000 --fees=1ulava
```

Delegate

```bash
lavad tx staking delegate <TO_VALOPER_ADDRESS> 1000000ulava --from $WALLET --chain-id lava-mainnet-1 --gas=350000 --fees=1ulava
```

Redelegate Stake to Another Validator

```bash
lavad tx staking redelegate $VALOPER_ADDRESS <TO_VALOPER_ADDRESS> 1000000ulava --from $WALLET --chain-id lava-mainnet-1 --gas=350000 --fees=1ulava
```

Unbond

```bash
lavad tx staking unbond $(lavad keys show $WALLET --bech val -a) 1000000ulava --from $WALLET --chain-id lava-mainnet-1 --gas=350000 --fees=1ulava
```

Transfer Funds

```bash
lavad tx bank send $WALLET_ADDRESS <TO_WALLET_ADDRESS> 1000000ulava --gas=350000 --fees=1ulava
```

### <mark style="color:red;">Validator operations</mark> <a href="#validator-operations" id="validator-operations"></a>

Create New Validator

```bash
lavad tx staking create-validator \
--amount 1000000ulava \
--from $WALLET \
--commission-rate 0.1 \
--commission-max-rate 0.2 \
--commission-max-change-rate 0.01 \
--min-self-delegation 1 \
--pubkey $(lavad tendermint show-validator) \
--moniker "$MONIKER" \
--identity "" \
--website ""
--details "xxxxxxxxxx" \
--contact "xxxxxxx"
--chain-id lava-mainnet-1 \
--gas=350000 \
--fees=1ulava \
-y 
```

Edit Existing Validator

```bash
lavad tx staking edit-validator \
--commission-rate 0.1 \
--new-moniker "$MONIKER" \
--identity "" \
--details "xxxxxxxxxx" \
--from $WALLET \
--chain-id lava-mainnet-1 \
--gas=350000 \
--fees=1ulava \
-y 
```

Validator info

```bash
lavad status 2>&1 | jq .ValidatorInfo
```

Validator Details

```bash
lavad q staking validator $(lavad keys show $WALLET --bech val -a)
```

Jailing info

```bash
lavad q slashing signing-info $(lavad tendermint show-validator)
```

Unjail validator

```bash
lavad tx slashing unjail --from $WALLET --chain-id lava-mainnet-1 --gas=350000 --fees=1ulava
```

Active Validators List

```bash
lavad q staking validators -oj --limit=2000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " 	 " + .description.moniker' | sort -gr | nl
```

Check Validator key

```bash
[[ $(lavad q staking validator $VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(lavad status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "Your key status is ok" || echo -e "Your key status is error"
```

Signing info

```bash
lavad q slashing signing-info $(lavad tendermint show-validator)
```

### <mark style="color:green;">Governance</mark> <a href="#governance" id="governance"></a>

Create New Text Proposal

```bash
lavad  tx gov submit-proposal \
--title "" \
--description "" \
--deposit 1000000ulava \
--type Text \
--from $WALLET \
--gas=350000 \
--fees=1ulava \
-y 
```

Proposals List

```bash
lavad query gov proposals
```

Proposal ID

View proposal

```bash
lavad query gov proposal 1
```

Vote

```bash
lavad tx gov vote 1 yes --from $WALLET --chain-id lava-mainnet-1  --gas=350000 --fees=1ulava
```

#### <mark style="background-color:yellow;">Delete Node</mark>

```
sudo systemctl stop lavad
sudo systemctl disable lavad
sudo rm -rf /etc/systemd/system/lavad.service
sudo rm $(which lavad)
sudo rm -rf $HOME/.lava
sed -i "/LAVA_/d" $HOME/.bash_profile
```
