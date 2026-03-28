# Useful commands

### 🔑 Wallet Management

#### Create wallet

```
zigchaind keys add wallet
```

#### Recover wallet

```
zigchaind keys add wallet --recover
```

#### Show wallet address

```
zigchaind keys show wallet -a
```

#### Show validator address

```
zigchaind keys show wallet --bech val -a
```

#### List wallets

```
zigchaind keys list
```

***

### 💰 Balance & Account

#### Check balance

```
zigchaind query bank balances $(zigchaind keys show wallet -a) \
--node https://rpc.zigchain-mainnet.aknodes.net:443
```

#### Check account info

```
zigchaind query auth account $(zigchaind keys show wallet -a) \
--node https://rpc.zigchain-mainnet.aknodes.net:443
```

***

### 🔄 Transactions

#### Send tokens

```
zigchaind tx bank send wallet <TO_ADDRESS> 1000000uzig \
--chain-id zigchain-1 \
--fees 1000000uzig \
--gas auto \
--gas-adjustment 1.2 \
--node https://rpc.zigchain-mainnet.aknodes.net:443 \
-y
```

***

### 🏦 Staking

#### Create validator

```
zigchaind tx staking create-validator $HOME/.zigchain/validator.json \
--from wallet \
--chain-id zigchain-1 \
--fees 1000000uzig \
--gas auto \
--gas-adjustment 1.2 \
--node https://rpc.zigchain-mainnet.aknodes.net:443 \
-y
```

***

#### Delegate (stake)

```
zigchaind tx staking delegate \
$(zigchaind keys show wallet --bech val -a) \
20000000uzig \
--from wallet \
--chain-id zigchain-1 \
--fees 1000000uzig \
--gas auto \
--gas-adjustment 1.2 \
--node https://rpc.zigchain-mainnet.aknodes.net:443 \
-y
```

***

#### Redelegate

```
zigchaind tx staking redelegate \
<VALOPER_SRC> \
<VALOPER_DST> \
1000000uzig \
--from wallet \
--chain-id zigchain-1 \
--fees 1000000uzig \
--gas auto \
--gas-adjustment 1.2 \
--node https://rpc.zigchain-mainnet.aknodes.net:443 \
-y
```

***

#### Undelegate

```
zigchaind tx staking unbond \
$(zigchaind keys show wallet --bech val -a) \
1000000uzig \
--from wallet \
--chain-id zigchain-1 \
--fees 1000000uzig \
--gas auto \
--gas-adjustment 1.2 \
--node https://rpc.zigchain-mainnet.aknodes.net:443 \
-y
```

***

### 🎯 Validator Management

#### Edit validator

```
zigchaind tx staking edit-validator \
--moniker "<YOUR_MONIKER>" \
--identity "<YOUR_IDENTITY>" \
--website "<YOUR_WEBSITE>" \
--security-contact "<YOUR_EMAIL>" \
--details "<YOUR_DESCRIPTION>" \
--from wallet \
--chain-id zigchain-1 \
--fees 1000000uzig \
--gas auto \
--gas-adjustment 1.2 \
--node https://rpc.zigchain-mainnet.aknodes.net:443 \
-y
```

***

#### Unjail validator

```
zigchaind tx slashing unjail \
--from wallet \
--chain-id zigchain-1 \
--fees 1000000uzig \
--gas auto \
--gas-adjustment 1.2 \
--node https://rpc.zigchain-mainnet.aknodes.net:443 \
-y
```

***

### 📊 Queries

#### Check validator info

```
zigchaind query staking validator \
$(zigchaind keys show wallet --bech val -a) \
--node https://rpc.zigchain-mainnet.aknodes.net:443
```

***

#### List all validators

```
zigchaind query staking validators \
--node https://rpc.zigchain-mainnet.aknodes.net:443
```

***

#### Check delegations

```
zigchaind query staking delegations \
$(zigchaind keys show wallet -a) \
--node https://rpc.zigchain-mainnet.aknodes.net:443
```

***

#### Check rewards

```
zigchaind query distribution rewards \
$(zigchaind keys show wallet -a) \
--node https://rpc.zigchain-mainnet.aknodes.net:443
```

***

### 💸 Rewards

#### Withdraw rewards

```
zigchaind tx distribution withdraw-all-rewards \
--from wallet \
--chain-id zigchain-1 \
--fees 1000000uzig \
--gas auto \
--gas-adjustment 1.2 \
--node https://rpc.zigchain-mainnet.aknodes.net:443 \
-y
```

***

#### Withdraw rewards + commission

```
zigchaind tx distribution withdraw-rewards \
$(zigchaind keys show wallet --bech val -a) \
--commission \
--from wallet \
--chain-id zigchain-1 \
--fees 1000000uzig \
--gas auto \
--gas-adjustment 1.2 \
--node https://rpc.zigchain-mainnet.aknodes.net:443 \
-y
```

***

### 🧱 Node & Status

#### Check node status

```
zigchaind status
```

***

#### Check sync status

```
zigchaind status | jq .SyncInfo
```

***

#### Check logs

```
journalctl -u zigchaind -f -o cat
```

***

#### Restart node

```
sudo systemctl restart zigchaind
```
