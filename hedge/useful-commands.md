# Useful commands

### <mark style="color:yellow;">Keys</mark> :

*   add keys\


    ```
    hedged keys add wallet
    ```
*   delete keys\


    ```
    hedged keys delete wallet
    ```
*   recover keys from a seed passphrase\


    ```
    hedged keys add wallet --recover
    ```
*   list all keys\


    ```
    hedged keys list
    ```

###

*   send balance\


    ```
    hedged tx bank send wallet [TO_ADDRESS] [AMOUNT]uhedge \
    --chain-id berberis-1 \
    --from "wallet" \
    --gas "200000"
    ```


*   multi send balance\


    ```
    hedged tx bank multi-send wallet [TO_ADDRESS1] [TO_ADDRESS2] [TO_ADDRESS3] [AMOUNT]uhedge \
    --chain-id berberis-1 \
    --from "wallet" \
    --gas "200000"
    ```


*   create-validator\


    ```
    hedged tx staking create-validator \
    --amount 1000000uhedge \
    --pubkey $(hedged tendermint show-validator) \
    --chain-id berberis-1 \
    --min-self-delegation 1 \
    --commission-max-change-rate 0.01 \
    --commission-max-rate 0.2 \
    --commission-rate 0.05 \
    --moniker "VALIDATOR-MONIKER" \
    --identity "VALIDATOR-IDENTITY" \
    --details "VALIDATOR-DESCRIPTION" \
    --website "VALIDATOR-WEBSITE" \
    --security-contact ["VALIDATOR-CONTACT" \
    --from "wallet" \
    --gas "200000"
    ```


*   edit-validator\


    ```
    hedged tx staking edit-validator \
    --chain-id berberis-1 \
    --commission-rate 0.05 \
    --new-moniker ["NEW-VALIDATOR-MONIKER"] \
    --identity ["NEW-VALIDATOR-IDENTITY"] \
    --details ["NEW-VALIDATOR-DESCRIPTION"] \
    --website ["NEW-VALIDATOR-WEBSITE"] \
    --security-contact ["NEW-VALIDATOR-CONTACT"] \
    --from "wallet" \
    --gas "200000"
    ```


*   delegate / stake\


    ```
    hedged tx staking delegate [TO_VALOPER_ADDRESS] [AMOUNT]uhedge \
    --chain-id berberis-1 \
    --from "wallet" \
    --gas "200000"
    ```


*   redelegate\


    ```
    hedged tx staking redelegate [SRC_VALOPER_ADDRESS] [TO_VALOPER_ADDRESS] [AMOUNT]uhedge \
    --chain-id berberis-1 \
    --from "wallet" \
    --gas "200000"
    ```



### <mark style="color:red;">Slashing</mark> :

*   unjail-validator\


    ```
    hedged tx slashing unjail \
    --chain-id berberis-1 \
    --from "wallet" \
    --gas "200000"
    ```



### <mark style="color:orange;">Gov</mark> :

*   query all proposals:\


    ```
    hedged query gov proposals
    ```


*   query specific proposals by id:\


    ```
    hedged query gov proposal [PROPOSAL_ID]
    ```


*   vote proposal:

    OPTION\_VALUE: yes , no , no\_with\_veto and abstain

    ```
    hedged tx gov vote [PROPOSAL_ID] [OPTION] \
    --chain-id berberis-1 \
    --from "wallet" \
    --gas "200000"
    ```



### <mark style="color:purple;">Distribution</mark> :

*   Withdraw all rewards :\


    ```
    hedged tx distribution withdraw-all-rewards \
    --chain-id berberis-1 \
    --from "wallet" \
    --gas "200000"
    ```


*   Withdraw reward and commission:\


    ```
    hedged tx distribution withdraw-rewards $(hedged keys show wallet --bech val -a) \
    --commission \
    --chain-id berberis-1 \
    --from "wallet" \
    --gas "200000"
    ```
