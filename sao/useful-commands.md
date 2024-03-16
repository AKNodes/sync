# Useful commands

### <mark style="color:blue;">Service operations</mark> ⚙️ <a href="#service-operations" id="service-operations"></a>

Check logs

```bash
sudo journalctl -u saod -f
```

Start service

```bash
sudo systemctl start saod
```

Stop service

```bash
sudo systemctl stop saod
```

Restart service

```bash
sudo systemctl restart saod
```

Check service status

```bash
sudo systemctl status saod
```

Reload services

```bash
sudo systemctl daemon-reload
```

Enable Service

```bash
sudo systemctl enable saod
```

Disable Service

```bash
sudo systemctl disable saod
```

Sync info

```bash
saod status 2>&1 | jq .SyncInfo
```

Node info

```bash
saod status 2>&1 | jq .NodeInfo
```

Your node peer

```bash
echo $(saod tendermint show-node-id)'@'$(wget -qO- eth0.me)':'$(cat $HOME/.sao/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```

### <mark style="color:purple;">Key management</mark> <a href="#key-management" id="key-management"></a>

Add New Wallet

```bash
saod keys add $WALLET
```

Restore executing wallet

```bash
saod keys add $WALLET --recover
```

List All Wallets

```bash
saod keys list
```

Delete wallet

```bash
saod keys delete $WALLET
```

Check Balance

```bash
saod q bank balances $(mantrachaind keys show $WALLET -a)
```

Export Key (save to wallet.backup)

```bash
saod keys export $WALLET
```

Import Key (restore from wallet.backup)

```bash
saod keys import $WALLET wallet.backup
```

### <mark style="color:red;">Validator operations</mark> <a href="#validator-operations" id="validator-operations"></a>

Create New Validator

```bash
saod tx staking create-validator \
  --amount=1000000usct \
  --pubkey=$(saod tendermint show-validator) \
  --chain-id=sao-20230629 \
  --commission-rate="0.10" \
  --commission-max-rate="0.20" \
  --commission-max-change-rate="0.20" \
  --min-self-delegation=1 \
  --from mykey \
  --moniker="*YourMonikerName*" \
  --gas="2000000" \
  --gas-prices="0.0025usct" \
  --yes
```

Edit Existing Validator

```bash
saod tx staking edit-validator \
--commission-rate 0.10 \
--new-moniker "$MONIKER" \
--identity "" \
--details "xxxxxxxxx" \
--from $WALLET \
--chain-id sao-20230629 \
--gas auto --gas-adjustment 1.5 \
-y
```

Validator info

```bash
saod status 2>&1 | jq .ValidatorInfo
```

Validator Details

```bash
saod q staking validator $(saod keys show $WALLET --bech val -a)
```

Jailing info

```bash
saod q slashing signing-info $(saod tendermint show-validator)
```

Unjail validator

```bash
saod tx slashing unjail --from $WALLET --chain-id sao-20230629 --gas auto --gas-adjustment 1.5 -y
```

Active Validators List

```bash
saod q staking validators -oj --limit=2000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " 	 " + .description.moniker' | sort -gr | nl
```

Check Validator key

```bash
[[ $(saod q staking validator $VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(mantrachaind status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "Your key status is ok" || echo -e "Your key status is error"
```

Signing info

```bash
saod q slashing signing-info $(mantrachaind tendermint show-validator)
```

#### <mark style="background-color:green;">Delete Node</mark>

```
sudo systemctl stop saod
sudo systemctl disable saod
sudo rm -rf /etc/systemd/system/saod.service
sudo rm $(which saod)
sudo rm -rf $HOME/.sao
sed -i "/SAO_/d" $HOME/.bash_profile
```
