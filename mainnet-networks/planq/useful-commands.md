# Useful commands

### <mark style="color:blue;">Key Management</mark>

Add new key

```
planqd keys add wallet
```

Recover existing key

```
planqd keys add wallet --recover
```

List All key

```
planqd keys list
```

Delete key

```
planqd keys delete wallet
```

Export Key (save to wallet.backup)

```
planqd keys export wallet
```

Import key

```
planqd keys import wallet wallet.backup
```

Query Wallet Balance

```
planqd q bank balances $(planqd keys show wallet -a)
```

### <mark style="color:purple;">Validator Management</mark>

Create Validator

```sh
planqd tx staking create-validator \
  --amount "1000000000000000000aplanq" \
  --pubkey $(planqd tendermint show-validator) \
  --moniker "MONIKER" \
  --identity "KEYBASE_ID" \
  --details "YOUR DETAILS" \
  --website "YOUR WEBSITE" \
  --chain-id planq_7070-2 \
  --commission-rate "0.05" \
  --commission-max-rate "0.20" \
  --commission-max-change-rate "0.01" \
  --min-self-delegation "1" \
  --gas-prices="30000000000aplanq" \
  --gas="1000000" \
  --gas-adjustment="1.5" \
  --from wallet \
  -y
```

Edit Validator

```sh
planqd tx staking edit-validator \
--new-moniker="MONIKER" \
--identity="YOUR_KEYBASE_ID" \
--details="YOUR_DETAILS" \
--website="YOUR_WEBSITE_URL" \
--chain-id planq_7070-2 \
--commission-rate=0.05 \
--from=wallet \
--gas-adjustment="1.5" \
--gas="1000000" \
--gas-prices="30000000000aplanq" \
-y
```

Unjail Validator

```sh
planqd tx slashing unjail --from wallet --chain-id planq_7070-2 --gas-prices 30000000000aplanq --gas-adjustment 1.5 --gas 1000000 -y 
```

Signing Info

```sh
planqd query slashing signing-info $(planqd tendermint show-validator) 
```

List all active validators

```sh
planqd q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl 
```

List all inactive validators

```sh
planqd q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_UNBONDED") or .status=="BOND_STATUS_UNBONDING")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl 
```

View validators details

```
planqd q staking validator $(planqd keys show wallet --bech val -a) 
```

### <mark style="color:orange;">Token Management</mark>

Withdraw rewards from all validators

```sh
planqd tx distribution withdraw-all-rewards --from wallet --chain-id planq_7070-2 --gas-prices 30000000000aplanq --gas-adjustment 1.5 --gas 1000000 -y 
```

Withdraw comission and rewards from your validator

```sh
planqd tx distribution withdraw-rewards $(planqd keys show wallet --bech val -a) --commission --from wallet --chain-id planq_7070-2 --gas-prices 30000000000aplanq --gas-adjustment 1.5 --gas 1000000 -y 
```

Delegate to your validator

```sh
planqd tx staking delegate $(planqd keys show wallet --bech val -a) 1000000000000000000aplanq --from wallet --chain-id planq_7070-2 --gas-prices 30000000000aplanq --gas-adjustment 1.5 --gas 1000000 -y 
```

Delegate to other

```sh
planqd tx staking delegate TO_VALOPER_ADDRESS 1000000000000000000aplanq --from wallet --chain-id planq_7070-2 --gas-prices 30000000000aplanq --gas-adjustment 1.5 --gas 1000000 -y 
```

Redelegate your stake to other validators

```sh
planqd tx staking redelegate $(planqd keys show wallet --bech val -a) TO_VALOPER_ADDRESS 1000000000000000000aplanq --from wallet --chain-id planq_7070-2 --gas-prices 30000000000aplanq --gas-adjustment 1.5 --gas 1000000 -y 
```

Unbond stake

```sh
planqd tx staking unbond $(planqd keys show wallet --bech val -a) 1000000000000000000aplanq --from wallet --chain-id planq_7070-2 --gas-prices 30000000000aplanq --gas-adjustment 1.5 --gas 1000000 -y 
```

Send tokens

```sh
planqd tx bank send wallet TO_WALLET_ADDRESS 1000000000000000000aplanq --from wallet --chain-id planq_7070-2 --gas-prices 30000000000aplanq --gas-adjustment 1.5 --gas 1000000 -y 
```

### <mark style="color:red;">Governance</mark>

List all proposals

<pre class="language-sh"><code class="lang-sh"><strong>planqd query gov proposals
</strong></code></pre>

Vote **YES / NO / ABSTAIN / NO\_WITH\_VETO**

```sh
planqd tx gov vote 1 yes --from wallet --chain-id planq_7070-2 --gas-prices 30000000000aplanq --gas-adjustment 1.5 --gas 1000000 -y 
```

Create new text proposal

```sh
planqd tx gov submit-proposal \
--title="Title" \
--description="Description" \
--deposit=100000000000000000000aplanq \
--type="Text" \
--from=wallet \
--gas-prices 30000000000aplanq \ 
--gas-adjustment 1.5 \
--gas 1000000 \
-y 
```

### <mark style="color:yellow;">Utility</mark>

Set Indexer **NULL / KV**

```sh
sed -i 's|^indexer *=.*|indexer = "null"|' $HOME/.planqd/config/config.toml
```

Set Custom Port

```sh
CUSTOM_PORT=13
sed -i.bak -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${CUSTOM_PORT}658\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${CUSTOM_PORT}657\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${CUSTOM_PORT}060\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${CUSTOM_PORT}656\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${CUSTOM_PORT}660\"%" $HOME/.planqd/config/config.toml
sed -i.bak -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:${CUSTOM_PORT}317\"%; s%^address = \":8080\"%address = \":${CUSTOM_PORT}080\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${CUSTOM_PORT}090\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${CUSTOM_PORT}091\"%; s%^address = \"0.0.0.0:8545\"%address = \"0.0.0.0:${CUSTOM_PORT}545\"%; s%^ws-address = \"0.0.0.0:8546\"%ws-address = \"0.0.0.0:${CUSTOM_PORT}546\"%" $HOME/.planqd/config/app.toml
```

Get Validator info

```sh
planqd status 2>&1 | jq .ValidatorInfo
```

Get denom info

```sh
planqd q bank denom-metadata -oj | jq
```

Get sync status

```sh
planqd status 2>&1 | jq .SyncInfo.catching_up
```

Get latest height

```sh
planqd status 2>&1 | jq .SyncInfo.latest_block_height
```

Reset Node

```sh
planqd tendermint unsafe-reset-all --home $HOME/.planqd --keep-addr-book
```

Delete Node

```sh
sudo systemctl stop planqd && sudo systemctl disable planqd && sudo rm /etc/systemd/system/planqd.service && sudo systemctl daemon-reload && rm -rf $HOME/.planqd && sudo rm -rf $(which planqd) 
```

### <mark style="color:green;">Services Management</mark>

```sh
# Reload Service
sudo systemctl daemon-reload

# Enable Service
sudo systemctl enable planqd

# Disable Service
sudo systemctl disable planqd

# Start Service
sudo systemctl start planqd

# Stop Service
sudo systemctl stop planqd

# Restart Service
sudo systemctl restart planqd

# Check Service Status
sudo systemctl status planqd

# Check Service Logs
sudo journalctl -u planqd -f --no-hostname -o cat
```
