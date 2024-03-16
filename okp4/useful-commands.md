# Useful commands

### <mark style="color:green;">Service Management</mark>⚙️ <a href="#service-management" id="service-management"></a>

**RELOAD SERVICE CONFIGURATION**

```
sudo systemctl daemon-reload
```

**ENABLE SERVICE**

```
sudo systemctl enable okp4.service
```

**DISABLE SERVICE**

```
sudo systemctl disable okp4.service
```

**START SERVICE**

```
sudo systemctl start okp4.service
```

**STOP SERVICE**

```
sudo systemctl stop okp4.service
```

**RESTART SERVICE**

```
sudo systemctl restart okp4.service
```

**CHECK SERVICE STATUS**

```
sudo systemctl status okp4.service
```

**CHECK SERVICE LOGS**

```
sudo journalctl -u okp4.service -f --no-hostname -o cat
```

### Key management <a href="#key-management" id="key-management"></a>

**ADD NEW KEY**

```
okp4d keys add wallet
```

**RECOVER EXISTING KEY**

```
okp4d keys add wallet --recover
```

**LIST ALL KEYS**

```
okp4d keys list
```

**DELETE KEY**

```
okp4d keys delete wallet
```

**EXPORT KEY TO A FILE**

```
okp4d keys export wallet
```

**IMPORT KEY FROM A FILE**

```
okp4d keys import wallet wallet.backup
```

**QUERY WALLET BALANCE**

```
okp4d q bank balances $(okp4d keys show wallet -a)
```

### &#x20;<mark style="color:yellow;">Validator management</mark> <a href="#validator-management" id="validator-management"></a>

**CREATE NEW VALIDATOR**

```
okp4d tx staking create-validator \
--amount 1000000uknow \
--pubkey $(okp4d tendermint show-validator) \
--moniker "YOUR_MONIKER_NAME" \
--identity "YOUR_KEYBASE_ID" \
--details "YOUR_DETAILS" \
--website "YOUR_WEBSITE_URL" \
--chain-id okp4-nemeton-1 \
--commission-rate 0.05 \
--commission-max-rate 0.20 \
--commission-max-change-rate 0.01 \
--min-self-delegation 1 \
--from wallet \
--gas-adjustment 1.4 \
--gas auto \
--gas-prices 0uknow \
-y
```

**EDIT EXISTING VALIDATOR**

```
okp4d tx staking edit-validator \
--new-moniker "YOUR_MONIKER_NAME" \
--identity "YOUR_KEYBASE_ID" \
--details "YOUR_DETAILS" \
--website "YOUR_WEBSITE_URL" \
--chain-id okp4-nemeton-1 \
--commission-rate 0.05 \
--from wallet \
--gas-adjustment 1.4 \
--gas auto \
--gas-prices 0uknow \
-y
```

**UNJAIL VALIDATOR**

```
okp4d tx slashing unjail --from wallet --chain-id okp4-nemeton-1 --gas-adjustment 1.4 --gas auto --gas-prices 0uknow -y
```

**JAIL REASON**

```
okp4d query slashing signing-info $(okp4d tendermint show-validator)
```

**LIST ALL ACTIVE VALIDATORS**

```
okp4d q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

**LIST ALL INACTIVE VALIDATORS**

```
okp4d q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_UNBONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

**VIEW VALIDATOR DETAILS**

```
okp4d q staking validator $(okp4d keys show wallet --bech val -a)
```

### &#x20;<mark style="color:red;">Token management</mark> <a href="#token-management" id="token-management"></a>

**WITHDRAW REWARDS FROM ALL VALIDATORS**

```
okp4d tx distribution withdraw-all-rewards --from wallet --chain-id okp4-nemeton-1 --gas-adjustment 1.4 --gas auto --gas-prices 0uknow -y
```

**WITHDRAW COMMISSION AND REWARDS FROM YOUR VALIDATOR**

```
okp4d tx distribution withdraw-rewards $(okp4d keys show wallet --bech val -a) --commission --from wallet --chain-id okp4-nemeton-1 --gas-adjustment 1.4 --gas auto --gas-prices 0uknow -y
```

**DELEGATE TOKENS TO YOURSELF**

```
okp4d tx staking delegate $(okp4d keys show wallet --bech val -a) 1000000uknow --from wallet --chain-id okp4-nemeton-1 --gas-adjustment 1.4 --gas auto --gas-prices 0uknow -y
```

**DELEGATE TOKENS TO VALIDATOR**

```
okp4d tx staking delegate <TO_VALOPER_ADDRESS> 1000000uknow --from wallet --chain-id okp4-nemeton-1 --gas-adjustment 1.4 --gas auto --gas-prices 0uknow -y
```

**REDELEGATE TOKENS TO ANOTHER VALIDATOR**

```
okp4d tx staking redelegate $(okp4d keys show wallet --bech val -a) <TO_VALOPER_ADDRESS> 1000000uknow --from wallet --chain-id okp4-nemeton-1 --gas-adjustment 1.4 --gas auto --gas-prices 0uknow -y
```

**UNBOND TOKENS FROM YOUR VALIDATOR**

```
okp4d tx staking unbond $(okp4d keys show wallet --bech val -a) 1000000uknow --from wallet --chain-id okp4-nemeton-1 --gas-adjustment 1.4 --gas auto --gas-prices 0uknow -y
```

**SEND TOKENS TO THE WALLET**

```
okp4d tx bank send wallet <TO_WALLET_ADDRESS> 1000000uknow --from wallet --chain-id okp4-nemeton-1 --gas-adjustment 1.4 --gas auto --gas-prices 0uknow -y
```

### &#x20;<mark style="color:orange;">Governance</mark> <a href="#governance" id="governance"></a>

**LIST ALL PROPOSALS**

```
okp4d query gov proposals
```

**VIEW PROPOSAL BY ID**

```
okp4d query gov proposal 1
```

**VOTE ‘YES’**

```
okp4d tx gov vote 1 yes --from wallet --chain-id okp4-nemeton-1 --gas-adjustment 1.4 --gas auto --gas-prices 0uknow -y
```

**VOTE ‘NO’**

```
okp4d tx gov vote 1 no --from wallet --chain-id okp4-nemeton-1 --gas-adjustment 1.4 --gas auto --gas-prices 0uknow -y
```

**VOTE ‘ABSTAIN’**

```
okp4d tx gov vote 1 abstain --from wallet --chain-id okp4-nemeton-1 --gas-adjustment 1.4 --gas auto --gas-prices 0uknow -y
```

**VOTE ‘NOWITHVETO’**

```
okp4d tx gov vote 1 NoWithVeto --from wallet --chain-id okp4-nemeton-1 --gas-adjustment 1.4 --gas auto --gas-prices 0uknow -y
```

### &#x20;<mark style="color:purple;">Utility</mark> <a href="#utility" id="utility"></a>

**UPDATE PORTS**

```
CUSTOM_PORT=110
sed -i -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${CUSTOM_PORT}58\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${CUSTOM_PORT}57\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${CUSTOM_PORT}60\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${CUSTOM_PORT}56\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${CUSTOM_PORT}66\"%" $HOME/.okp4d/config/config.toml
sed -i -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:${CUSTOM_PORT}17\"%; s%^address = \":8080\"%address = \":${CUSTOM_PORT}80\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${CUSTOM_PORT}90\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${CUSTOM_PORT}91\"%" $HOME/.okp4d/config/app.toml
```

**UPDATE INDEXER**

**Disable indexer**

```
sed -i -e 's|^indexer *=.*|indexer = "null"|' $HOME/.okp4d/config/config.toml
```

**Enable indexer**

```
sed -i -e 's|^indexer *=.*|indexer = "kv"|' $HOME/.okp4d/config/config.toml
```

**UPDATE PRUNING**

```
sed -i \
  -e 's|^pruning *=.*|pruning = "custom"|' \
  -e 's|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|' \
  -e 's|^pruning-keep-every *=.*|pruning-keep-every = "0"|' \
  -e 's|^pruning-interval *=.*|pruning-interval = "19"|' \
  $HOME/.okp4d/config/app.toml
```

### &#x20;<mark style="color:blue;">Maintenance</mark> <a href="#maintenance" id="maintenance"></a>

**GET VALIDATOR INFO**

```
okp4d status 2>&1 | jq .ValidatorInfo
```

**GET SYNC INFO**

```
okp4d status 2>&1 | jq .SyncInfo
```

**GET NODE PEER**

```
echo $(okp4d tendermint show-node-id)'@'$(curl -s ifconfig.me)':'$(cat $HOME/.okp4d/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```

**CHECK IF VALIDATOR KEY IS CORRECT**

```
[[ $(okp4d q staking validator $(okp4d keys show wallet --bech val -a) -oj | jq -r .consensus_pubkey.key) = $(okp4d status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

**GET LIVE PEERS**

```
curl -sS http://localhost:13657/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}'
```

**SET MINIMUM GAS PRICE**

```
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0uknow\"/" $HOME/.okp4d/config/app.toml
```

**ENABLE PROMETHEUS**

```
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.okp4d/config/config.toml
```

**RESET CHAIN DATA**

```
okp4d tendermint unsafe-reset-all --keep-addr-book --home $HOME/.okp4d --keep-addr-book
```

**REMOVE NODE**

```
cd $HOME
sudo systemctl stop okp4.service
sudo systemctl disable okp4.service
sudo rm /etc/systemd/system/okp4.service
sudo systemctl daemon-reload
rm -f $(which okp4d)
rm -rf $HOME/.okp4d
rm -rf $HOME/okp4d
```
