# Useful Commands

### Key management 🔑

**Add new key**

```html
symphonyd keys add wallet
```

**Recover existing key**

```html
symphonyd keys add wallet --recover
```

**List all keys**

```html
symphonyd keys list
```

**Delete key**

```html
symphonyd keys delete wallet
```

**Export key to the file**

```html
symphonyd keys export wallet
```

**Import key from the file**

```html
symphonyd keys import wallet wallet.backup
```

**Query wallet balance**

```html
symphonyd q bank balances $(symphonyd keys show wallet -a)

```

### Validator management 👷 

**Create new validator**

```html
symphonyd tx staking create-validator --amount 1000000note --pubkey $(symphonyd tendermint show-validator) --moniker "YOUR_MONIKER_NAME" --identity "YOUR_KEYBASE_ID" --details "YOUR_DETAILS" --website "YOUR_WEBSITE_URL" --chain-id symphony-testnet-2 --commission-rate 0.05 --commission-max-rate 0.20 --commission-max-change-rate 0.01 --min-self-delegation 1 --from wallet --gas-adjustment 1.4 --gas auto --gas-prices 0.025note -y
```

**Edit existing validator**

```html
symphonyd tx staking edit-validator --new-moniker "YOUR_MONIKER_NAME" --identity "YOUR_KEYBASE_ID" --details "YOUR_DETAILS" --website "YOUR_WEBSITE_URL" --chain-id symphony-testnet-2 --commission-rate 0.05 --from wallet --gas-adjustment 1.4 --gas auto --gas-prices 0.025note -y
```

**Unjail validator**

```html
symphonyd tx slashing unjail --from wallet --chain-id symphony-testnet-2 --gas-adjustment 1.4 --gas auto --gas-prices 0.025note -y
```

**Jail reason**

```html
symphonyd query slashing signing-info $(symphonyd tendermint show-validator)
```

**List all active validators**

```html
symphonyd q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " 	 " + .description.moniker' | sort -gr | nl
```

**List all inactive validators**

```html
symphonyd q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_UNBONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " 	 " + .description.moniker' | sort -gr | nl
```

**View validator details**

```html
symphonyd q staking validator $(symphonyd keys show wallet --bech val -a)
```

### Token management 💲

**Withdraw rewards from all validators**

```html
symphonyd tx distribution withdraw-all-rewards --from wallet --chain-id symphony-testnet-2 --gas-adjustment 1.4 --gas auto --gas-prices 0.025note -y
```

**Withdraw commission and rewards from your validator**

```html
symphonyd tx distribution withdraw-rewards $(symphonyd keys show wallet --bech val -a) --commission --from wallet --chain-id symphony-testnet-2 --gas-adjustment 1.4 --gas auto --gas-prices 0.025note -y
```

**Delegate tokens to yourself**

```html
symphonyd tx staking delegate $(symphonyd keys show wallet --bech val -a) 1000000note --from wallet --chain-id symphony-testnet-2 --gas-adjustment 1.4 --gas auto --gas-prices 0.025note -y
```

**Delegate tokens to validator**

```html
symphonyd tx staking delegate TO_VALOPER_ADDRESS 1000000note --from wallet --chain-id symphony-testnet-2 --gas-adjustment 1.4 --gas auto --gas-prices 0.025note -y
```

**Redelegate tokens to another validator**

```html
symphonyd tx staking redelegate $(symphonyd keys show wallet --bech val -a) TO_VALOPER_ADDRESS 1000000note --from wallet --chain-id symphony-testnet-2 --gas-adjustment 1.4 --gas auto --gas-prices 0.025note -y
```

**Unbond tokens from your validator**

```html
symphonyd tx staking unbond $(symphonyd keys show wallet --bech val -a) 1000000note --from wallet --chain-id symphony-testnet-2 --gas-adjustment 1.4 --gas auto --gas-prices 0.025note -y
```

**Send tokens to the wallet**

```html
symphonyd tx bank send wallet TO_WALLET_ADDRESS 1000000note --from wallet --chain-id symphony-testnet-2 --gas-adjustment 1.4 --gas auto --gas-prices 0.025note -y
```

### Governance 🗳

**List all proposals**

```html
symphonyd query gov proposals
```

**View proposal by id**

```html
symphonyd query gov proposal 1
```

**Vote 'Yes'**

```html
symphonyd tx gov vote 1 yes --from wallet --chain-id symphony-testnet-2 --gas-adjustment 1.4 --gas auto --gas-prices 0.025note -y
```

**Vote 'No'**

```html
symphonyd tx gov vote 1 no --from wallet --chain-id symphony-testnet-2 --gas-adjustment 1.4 --gas auto --gas-prices 0.025note -y
```

**Vote 'Abstain'**

```html
symphonyd tx gov vote 1 abstain --from wallet --chain-id symphony-testnet-2 --gas-adjustment 1.4 --gas auto --gas-prices 0.025note -y
```

**Vote 'NoWithVeto'**

```html
symphonyd tx gov vote 1 NoWithVeto --from wallet --chain-id symphony-testnet-2 --gas-adjustment 1.4 --gas auto --gas-prices 0.025note -y
```

### Utility ⚡️

**Update Indexer**

```html
sed -i -e 's|^indexer *=.*|indexer = "null"|' $HOME/.symphonyd/config/config.toml

```

**Update pruning**

```html
sed -i   -e 's|^pruning *=.*|pruning = "custom"|'   -e 's|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|'   -e 's|^pruning-keep-every *=.*|pruning-keep-every = "0"|'   -e 's|^pruning-interval *=.*|pruning-interval = "19"|'   $HOME/.symphonyd/config/app.toml
```

### Maintenance 🚨

**Get validator info**

```html
symphonyd status 2>&1 | jq .ValidatorInfo
```

**Get sync info**

```html
symphonyd status 2>&1 | jq .SyncInfo
```

**Get node peer**

```html
echo $(symphonyd tendermint show-node-id)'@'$(curl -s ifconfig.me)':'$(cat $HOME/.symphonyd/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```

**Get live peers**

```html
curl -sS http://localhost:27657/net_info | jq -r '.result.peers[] | "(.node_info.id)@(.remote_ip):(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}'
```

**Set minimum gas price**

```html
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = "0.025note"/" $HOME/.symphonyd/config/app.toml
```

**Enable prometheus**

```html
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.symphonyd/config/config.toml
```

**Reset chain data**

```html
symphonyd tendermint unsafe-reset-all --home $HOME/.symphonyd --keep-addr-book
```

**Remove node**

```html
cd $HOME
sudo systemctl stop symphonyd
sudo systemctl disable symphonyd
sudo rm /etc/systemd/system/symphonyd.service
sudo systemctl daemon-reload
rm -f $(which symphonyd)
rm -rf $HOME/.symphonyd
rm -rf $HOME/symphony
```



### Service Management ⚙️

**Reload service configuration**

```html
sudo systemctl daemon-reload
```

**Enable service**

```html
sudo systemctl enable symphonyd
```

**Disable service**

```html
sudo systemctl disable symphonyd
```

**Start service**

```html
sudo systemctl start symphonyd
```

**Stop service**

```html
sudo systemctl stop symphonyd
```

**Restart service**

```html
sudo systemctl restart symphonyd
```

**Check service status**

```html
sudo systemctl status symphonyd
```

**Check service logs**

```html
sudo journalctl -u symphonyd -f --no-hostname -o cat
```
