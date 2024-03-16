# Useful commands

### <mark style="color:blue;">Service operations</mark> ⚙️ <a href="#service-operations" id="service-operations"></a>

Check logs

```bash
sudo journalctl -u mantrachaind -f
```

Start service

```bash
sudo systemctl start mantrachaind
```

Stop service

```bash
sudo systemctl stop mantrachaind
```

Restart service

```bash
sudo systemctl restart mantrachaind
```

Check service status

```bash
sudo systemctl status mantrachaind
```

Reload services

```bash
sudo systemctl daemon-reload
```

Enable Service

```bash
sudo systemctl enable mantrachaind
```

Disable Service

```bash
sudo systemctl disable mantrachaind
```

Sync info

```bash
mantrachaind status 2>&1 | jq .SyncInfo
```

Node info

```bash
mantrachaind status 2>&1 | jq .NodeInfo
```

Your node peer

```bash
echo $(mantrachaind tendermint show-node-id)'@'$(wget -qO- eth0.me)':'$(cat $HOME/.mantrachain/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```

### <mark style="color:purple;">Key management</mark> <a href="#key-management" id="key-management"></a>

Add New Wallet

```bash
mantrachaind keys add $WALLET
```

Restore executing wallet

```bash
mantrachaind keys add $WALLET --recover
```

List All Wallets

```bash
mantrachaind keys list
```

Delete wallet

```bash
mantrachaind keys delete $WALLET
```

Check Balance

```bash
mantrachaind q bank balances $(mantrachaind keys show $WALLET -a)
```

Export Key (save to wallet.backup)

```bash
mantrachaind keys export $WALLET
```

Import Key (restore from wallet.backup)

```bash
mantrachaind keys import $WALLET wallet.backup
```

### <mark style="color:orange;">Tokens</mark> <a href="#tokens" id="tokens"></a>

Withdraw all rewards

```bash
mantrachaind tx distribution withdraw-all-rewards --from $WALLET --chain-id mantrachain-testnet-1 --gas auto --gas-adjustment 1.5
```

Withdraw rewards and commission from your validator

```bash
mantrachaind tx distribution withdraw-rewards $VALOPER_ADDRESS --from $WALLET --commission --chain-id mantrachain-testnet-1 --gas auto --gas-adjustment 1.5 -y
```

Check your balance  &#x20;

```bash
mantrachaind query bank balances $WALLET_ADDRESS
```

Delegate to Yourself

```bash
mantrachaind tx staking delegate $(mantrachaind keys show $WALLET --bech val -a) 1000000uaum --from $WALLET --chain-id mantrachain-testnet-1 --gas auto --gas-adjustment 1.5 -y
```

Delegate

```bash
mantrachaind tx staking delegate <TO_VALOPER_ADDRESS> 1000000uaum --from $WALLET --chain-id mantrachain-testnet-1 --gas auto --gas-adjustment 1.5 -y
```

Redelegate Stake to Another Validator

```bash
mantrachaind tx staking redelegate $VALOPER_ADDRESS <TO_VALOPER_ADDRESS> 1000000uaum --from $WALLET --chain-id mantrachain-testnet-1 --gas auto --gas-adjustment 1.5 -y
```

Unbond

```bash
mantrachaind tx staking unbond $(mantrachaind keys show $WALLET --bech val -a) 1000000uaum --from $WALLET --chain-id mantrachain-testnet-1 --gas auto --gas-adjustment 1.5 -y
```

Transfer Funds

```bash
mantrachaind tx bank send $WALLET_ADDRESS <TO_WALLET_ADDRESS> 1000000uaum --gas auto --gas-adjustment 1.5 -y
```

### <mark style="color:red;">Validator operations</mark> <a href="#validator-operations" id="validator-operations"></a>

Create New Validator

```bash
mantrachaind tx staking create-validator \
--amount 1000000uaum \
--from $WALLET \
--commission-rate 0.1 \
--commission-max-rate 0.2 \
--commission-max-change-rate 0.01 \
--min-self-delegation 1 \
--pubkey $(mantrachaind tendermint show-validator) \
--moniker "$MONIKER" \
--identity "" \
--details "xxxxxxxxx" \
--chain-id mantrachain-testnet-1 \
--gas auto --gas-adjustment 1.5 --fees 50uaum \
-y
```

Edit Existing Validator

```bash
mantrachaind tx staking edit-validator \
--commission-rate 0.1 \
--new-moniker "$MONIKER" \
--identity "" \
--details "xxxxxxxxx" \
--from $WALLET \
--chain-id mantrachain-testnet-1 \
--gas auto --gas-adjustment 1.5 --fees 50uaum \
-y
```

Validator info

```bash
mantrachaind status 2>&1 | jq .ValidatorInfo
```

Validator Details

```bash
mantrachaind q staking validator $(mantrachaind keys show $WALLET --bech val -a)
```

Jailing info

```bash
mantrachaind q slashing signing-info $(mantrachaind tendermint show-validator)
```

Unjail validator

```bash
mantrachaind tx slashing unjail --from $WALLET --chain-id mantrachain-testnet-1 --gas auto --gas-adjustment 1.5 -y
```

Active Validators List

```bash
mantrachaind q staking validators -oj --limit=2000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " 	 " + .description.moniker' | sort -gr | nl
```

Check Validator key

```bash
[[ $(mantrachaind q staking validator $VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(mantrachaind status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "Your key status is ok" || echo -e "Your key status is error"
```

Signing info

```bash
mantrachaind q slashing signing-info $(mantrachaind tendermint show-validator)
```

### <mark style="color:yellow;">Governance</mark> <a href="#governance" id="governance"></a>

Create New Text Proposal

```bash
mantrachaind  tx gov submit-proposal \
--title "" \
--description "" \
--deposit 1000000uaum \
--type Text \
--from $WALLET \
--gas auto --gas-adjustment 1.5 \
-y 
```

Proposals List

```bash
mantrachaind query gov proposals
```

Proposal IDProposal optionYesNoNo with vetoAbstain

View proposal

```bash
mantrachaind query gov proposal 1
```

Vote

```bash
mantrachaind tx gov vote 1 yes --from $WALLET --chain-id mantrachain-testnet-1  --gas auto --gas-adjustment 1.5 -y
```



#### <mark style="background-color:green;">Delete Node</mark>

```
sudo systemctl stop mantrachaind
sudo systemctl disable mantrachaind
sudo rm -rf /etc/systemd/system/mantrachaind.service
sudo rm $(which mantrachaind)
sudo rm -rf $HOME/.mantrachain
sed -i "/MANTRA_/d" $HOME/.bash_profile
```
