# Useful commands

### **📌 Service Operations**

#### **Check logs**

```bash
sudo journalctl -u wardend -fo cat
```

#### **Start service**

```bash
sudo systemctl start wardend
```

#### **Stop service**

```bash
sudo systemctl stop wardend
```

#### **Restart service**

```bash
sudo systemctl restart wardend
```

#### **Check service status**

```bash
sudo systemctl status wardend
```

#### **Reload systemd**

```bash
sudo systemctl daemon-reload
```

#### **Enable service (auto-start)**

```bash
sudo systemctl enable wardend
```

#### **Disable service**

```bash
sudo systemctl disable wardend
```

***

## **📡 Node Information**

#### **Node status**

```bash
wardend status 2>&1 | jq
```

***

## **🔑 Wallet Management**

#### **Add a new wallet**

```bash
wardend keys add $WALLET
```

#### **Recover a wallet**

```bash
wardend keys add $WALLET --recover
```

#### **List all wallets**

```bash
wardend keys list
```

#### **Delete a wallet**

```bash
wardend keys delete $WALLET
```

#### **Check wallet balance**

```bash
wardend q bank balances $WALLET_ADDRESS
```

#### **Export wallet key**

```bash
wardend keys export $WALLET > wallet.backup
```

#### **View EVM private key**

```bash
wardend keys unsafe-export-eth-key $WALLET
```

#### **Import key from backup**

```bash
wardend keys import $WALLET wallet.backup
```

***

## **💰 Tokens / Staking Operations**

#### **Withdraw all rewards**

```bash
wardend tx distribution withdraw-all-rewards \
  --from $WALLET \
  --chain-id warden_8765-1 \
  --gas auto --gas-adjustment 1.6 \
  --fees 250000000000000award -y
```

#### **Withdraw rewards + commission**

```bash
wardend tx distribution withdraw-rewards $VALOPER_ADDRESS \
  --commission \
  --from $WALLET \
  --chain-id warden_8765-1 \
  --gas auto --gas-adjustment 1.6 \
  --fees 250000000000000award -y
```

#### **Delegate to your own validator**

```bash
wardend tx staking delegate \
  $(wardend keys show $WALLET --bech val -a) \
  1000000award \
  --from $WALLET \
  --chain-id warden_8765-1 \
  --gas auto --gas-adjustment 1.6 \
  --fees 250000000000000award -y
```

#### **Delegate to another validator**

```bash
wardend tx staking delegate <TO_VALOPER_ADDRESS> \
  1000000award \
  --from $WALLET \
  --chain-id warden_8765-1 \
  --gas auto --gas-adjustment 1.6 \
  --fees 250000000000000award -y
```

#### **Redelegate stake**

```bash
wardend tx staking redelegate \
  $VALOPER_ADDRESS <TO_VALOPER_ADDRESS> \
  1000000award \
  --from $WALLET \
  --chain-id warden_8765-1 \
  --gas auto --gas-adjustment 1.6 \
  --fees 250000000000000award -y
```

#### **Unbond stake**

```bash
wardend tx staking unbond \
  $(wardend keys show $WALLET --bech val -a) \
  1000000award \
  --from $WALLET \
  --chain-id warden_8765-1 \
  --gas auto --gas-adjustment 1.6 \
  --fees 250000000000000award -y
```

#### **Send tokens**

```bash
wardend tx bank send $WALLET_ADDRESS <TO_WALLET_ADDRESS> \
  1000000award \
  --gas auto --gas-adjustment 1.6 \
  --fees 250000000000000award -y
```

***

## **🧱 Validator Operations**

### **Create a new validator**

```bash
wardend tx staking create-validator \
  --amount 1000000award \
  --from $WALLET \
  --commission-rate 0.1 \
  --commission-max-rate 0.2 \
  --commission-max-change-rate 0.01 \
  --min-self-delegation 1 \
  --pubkey $(wardend tendermint show-validator) \
  --moniker "$MONIKER" \
  --identity "xxxxxxxxx" \
  --details "xxxxxxxxxx" \
  --chain-id warden_8765-1 \
  --gas auto --gas-adjustment 1.6 \
  --fees 250000000000000award \
  -y
```

### **Edit your validator**

```bash
wardend tx staking edit-validator \
  --commission-rate 0.1 \
  --new-moniker "$MONIKER" \
  --identity "xxxxxxxxxxx" \
  --details "xxxxxxxxxxxx" \
  --from $WALLET \
  --chain-id warden_8765-1 \
  --gas auto --gas-adjustment 1.6 \
  --fees 250000000000000award \
  -y
```

***

## **📋 Validator Status**

#### **Get validator details**

```bash
wardend q staking validator \
  $(wardend keys show $WALLET --bech val -a)
```

#### **Check jailing info**

```bash
wardend q slashing signing-info $(wardend tendermint show-validator)
```

#### **Slashing parameters**

```bash
wardend q slashing params
```

#### **Unjail validator**

```bash
wardend tx slashing unjail \
  --from $WALLET \
  --chain-id warden_8765-1 \
  --gas auto --gas-adjustment 1.6 \
  --fees 250000000000000award -y
```

#### **Active validators list**

```bash
wardend q staking validators -oj --limit=2000 \
| jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' \
| jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + "   " + .description.moniker' \
| sort -gr | nl
```

#### **Verify validator consensus key**

```bash
[[ $(wardend q staking validator $VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = \
$(wardend status | jq -r .ValidatorInfo.PubKey.value) ]] \
&& echo "Your key status is OK" \
|| echo "Your key status is ERROR"
```

***

## **🗳 Governance**

#### **Vote on proposal**

```bash
wardend tx gov vote 1 yes \
  --from $WALLET \
  --chain-id warden_8765-1 \
  --gas auto --gas-adjustment 1.6 \
  --fees 250000000000000award -y
```
