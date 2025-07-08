# Useful Commands

### 🔑 Key management <a href="#key-management" id="key-management"></a>

&#x20;**Add new key**

```
axoned keys add wallet
```

&#x20;**Recover existing key**

```
axoned keys add wallet --recover
```

&#x20;**List all keys**

```
axoned keys list
```

&#x20;**Delete key**

```
axoned keys delete wallet
```

&#x20;**Export key to a file**

```
axoned keys export wallet
```

&#x20;**Import key from a file**

```
axoned keys import wallet wallet.backup
```

&#x20;**Query wallet balance**

```
axoned q bank balances $(axoned keys show wallet -a)
```

### &#x20;👷 Validator management <a href="#validator-management" id="validator-management"></a>

&#x20;**Create new validator**

```
axoned tx staking create-validator <(cat <<EOF
{
  "pubkey": $(axoned comet show-validator),
  "amount": "1000000uaxone",
  "moniker": "YOUR_MONIKER_NAME",
  "identity": "YOUR_KEYBASE_ID",
  "website": "YOUR_WEBSITE_URL",
  "security": "YOUR_SECURITY_EMAIL",
  "details": "YOUR_DETAILS",
  "commission-rate": "0.05",
  "commission-max-rate": "0.20",
  "commission-max-change-rate": "0.05",
  "min-self-delegation": "1"
}
EOF
) \
--chain-id axone-1 \
--from wallet \
--gas-adjustment 1.4 \
--gas auto \
--gas-prices 0uaxone \
-y
```

&#x20;**Edit existing validator**

```
axoned tx staking edit-validator \
--new-moniker "YOUR_MONIKER_NAME" \
--identity "YOUR_KEYBASE_ID" \
--details "YOUR_DETAILS" \
--website "YOUR_WEBSITE_URL" \
--chain-id axone-1 \
--commission-rate 0.05 \
--from wallet \
--gas-adjustment 1.4 \
--gas auto \
--gas-prices 0uaxone \
-y
```

&#x20;**Unjail validator**

```
axoned tx slashing unjail --from wallet --chain-id axone-1 --gas-adjustment 1.4 --gas auto --gas-prices 0uaxone -y
```

&#x20;**Jail reason**

```
axoned query slashing signing-info $(axoned comet show-validator)
```

&#x20;**List all active validators**

```
axoned q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

&#x20;**List all inactive validators**

```
axoned q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_UNBONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

&#x20;**View validator details**

```
axoned q staking validator $(axoned keys show wallet --bech val -a)
```

### &#x20;💲 Token management <a href="#token-management" id="token-management"></a>

&#x20;**Withdraw rewards from all validators**

```
axoned tx distribution withdraw-all-rewards --from wallet --chain-id axone-1 --gas-adjustment 1.4 --gas auto --gas-prices 0uaxone -y
```

&#x20;**Withdraw commission and rewards from your validator**

```
axoned tx distribution withdraw-rewards $(axoned keys show wallet --bech val -a) --commission --from wallet --chain-id axone-1 --gas-adjustment 1.4 --gas auto --gas-prices 0uaxone -y
```

&#x20;**Delegate tokens to yourself**

```
axoned tx staking delegate $(axoned keys show wallet --bech val -a) 1000000uaxone --from wallet --chain-id axone-1 --gas-adjustment 1.4 --gas auto --gas-prices 0uaxone -y
```

&#x20;**Delegate tokens to validator**

```
axoned tx staking delegate <TO_VALOPER_ADDRESS> 1000000uaxone --from wallet --chain-id axone-1 --gas-adjustment 1.4 --gas auto --gas-prices 0uaxone -y
```

&#x20;**Redelegate tokens to another validator**

```
axoned tx staking redelegate $(axoned keys show wallet --bech val -a) <TO_VALOPER_ADDRESS> 1000000uaxone --from wallet --chain-id axone-1 --gas-adjustment 1.4 --gas auto --gas-prices 0uaxone -y
```

&#x20;**Unbond tokens from your validator**

```
axoned tx staking unbond $(axoned keys show wallet --bech val -a) 1000000uaxone --from wallet --chain-id axone-1 --gas-adjustment 1.4 --gas auto --gas-prices 0uaxone -y
```

&#x20;**Send tokens to the wallet**

```
axoned tx bank send wallet <TO_WALLET_ADDRESS> 1000000uaxone --from wallet --chain-id axone-1 --gas-adjustment 1.4 --gas auto --gas-prices 0uaxone -y
```

### &#x20;🗳 Governance <a href="#governance" id="governance"></a>

&#x20;**List all proposals**

```
axoned query gov proposals
```

&#x20;**View proposal by id**

```
axoned query gov proposal 1
```

&#x20;**Vote ‘Yes’**

```
axoned tx gov vote 1 yes --from wallet --chain-id axone-1 --gas-adjustment 1.4 --gas auto --gas-prices 0uaxone -y
```

&#x20;**Vote ‘No’**

```
axoned tx gov vote 1 no --from wallet --chain-id axone-1 --gas-adjustment 1.4 --gas auto --gas-prices 0uaxone -y
```

&#x20;**Vote ‘Abstain’**

```
axoned tx gov vote 1 abstain --from wallet --chain-id axone-1 --gas-adjustment 1.4 --gas auto --gas-prices 0uaxone -y
```

&#x20;**Vote ‘NoWithVeto’**

```
axoned tx gov vote 1 NoWithVeto --from wallet --chain-id axone-1 --gas-adjustment 1.4 --gas auto --gas-prices 0uaxone -y
```

### &#x20;⚡️ Utility <a href="#utility" id="utility"></a>

&#x20;**Update ports**

```
CUSTOM_PORT=110
sed -i -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${CUSTOM_PORT}58\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${CUSTOM_PORT}57\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${CUSTOM_PORT}60\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${CUSTOM_PORT}56\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${CUSTOM_PORT}66\"%" $HOME/.axoned/config/config.toml
sed -i -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:${CUSTOM_PORT}17\"%; s%^address = \":8080\"%address = \":${CUSTOM_PORT}80\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${CUSTOM_PORT}90\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${CUSTOM_PORT}91\"%" $HOME/.axoned/config/app.toml
```

&#x20;**Update Indexer**

**Disable indexer**

```
sed -i -e 's|^indexer *=.*|indexer = "null"|' $HOME/.axoned/config/config.toml
```

**Enable indexer**

```
sed -i -e 's|^indexer *=.*|indexer = "kv"|' $HOME/.axoned/config/config.toml
```

&#x20;**Update pruning**

```
sed -i \
  -e 's|^pruning *=.*|pruning = "custom"|' \
  -e 's|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|' \
  -e 's|^pruning-keep-every *=.*|pruning-keep-every = "0"|' \
  -e 's|^pruning-interval *=.*|pruning-interval = "19"|' \
  $HOME/.axoned/config/app.toml
```

### &#x20;🚨 Maintenance <a href="#maintenance" id="maintenance"></a>

&#x20;**Get validator info**

```
axoned status 2>&1 | jq .ValidatorInfo
```

&#x20;**Get sync info**

```
axoned status 2>&1 | jq .SyncInfo
```

&#x20;**Get node peer**

```
echo $(axoned comet show-node-id)'@'$(curl -4s ifconfig.me)':'$(cat $HOME/.axoned/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```

&#x20;**Check if validator key is correct**

```
[[ $(axoned q staking validator $(axoned keys show wallet --bech val -a) -oj | jq -r .consensus_pubkey.key) = $(axoned status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

&#x20;**Get live peers**

```
curl -sS http://localhost:13657/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}'
```

&#x20;**Set minimum gas price**

```
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0uaxone\"/" $HOME/.axoned/config/app.toml
```

&#x20;**Enable prometheus**

```
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.axoned/config/config.toml
```

&#x20;**Reset chain data**

```
axoned comet unsafe-reset-all --keep-addr-book --home $HOME/.axoned --keep-addr-book
```

&#x20;**Remove node**

```
cd $HOME
sudo systemctl stop axone.service
sudo systemctl disable axone.service
sudo rm /etc/systemd/system/axone.service
sudo systemctl daemon-reload
rm -f $(which axoned)
rm -rf $HOME/.axoned
rm -rf $HOME/axoned
```

### &#x20;⚙️ Service Management <a href="#service-management" id="service-management"></a>

&#x20;**Reload service configuration**

```
sudo systemctl daemon-reload
```

&#x20;**Enable service**

```
sudo systemctl enable axone.service
```

&#x20;**Disable service**

```
sudo systemctl disable axone.service
```

&#x20;**Start service**

```
sudo systemctl start axone.service
```

&#x20;**Stop service**

```
sudo systemctl stop axone.service
```

&#x20;**Restart service**

```
sudo systemctl restart axone.service
```

&#x20;**Check service status**

```
sudo systemctl status axone.service
```

&#x20;**Check service logs**

```
sudo journalctl -u axone.service -f --no-hostname -o cat
```
