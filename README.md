# Shentu-Install_Node

# Auto install

    wget -O ctk https://raw.githubusercontent.com/NodeValidatorVN/GuideNode/main/Shentu/ctk && chmod +x ctk && ./ctk

 Get Sync Status
 
    shentud status 2>&1 | jq .SyncInfo.catching_up``

                
# Add New Key

    celestia-appd keys add wallet

Query Wallet Balance

    celestia-appd q bank balances $(celestia-appd keys show wallet -a)

# Create New Validator

    celestia-appd tx staking create-validator \
    --amount=1000000utia \
    --pubkey=$(celestia-appd tendermint show-validator) \
    --chain-id=mocha-4 \
    --commission-rate="0.05" \
    --commission-max-rate="0.20" \
    --commission-max-change-rate="0.20" \
    --min-self-delegation=1 \
    --from mykey \
    --yes \
    --gas-adjustment=1.2 \
    --gas=auto \
    --gas-prices=0.005utia \
    --evm-address=$EVM_ADDRESS \
    --moniker="YOUR_MONIKER_NAME" \
    --website="YOUR_WEBSITE" \
    --security-contact="YOUR_EMAIL" \
    --details="YOUR_DETAIL"

  Reload systemD

     sudo systemctl enable shentud 
     sudo systemctl daemon-reload
     sudo systemctl restart shentud && journalctl -u shentud -f --no-hostname -o cat     

 Export Key (save to wallet.backup)

        shentud keys export wallet

Import Key

        shentud keys import wallet wallet.backup

Unjail Validator

        shentud tx slashing unjail --from wallet --chain-id shentu-2.2 --gas-prices 0uctk --gas-adjustment 1.5 --gas auto -y

Withdraw rewards & commission validator

        shentud tx distribution withdraw-rewards $(shentud keys show wallet --bech val -a) --commission --from wallet --chain-id shentu-2.2 --gas-prices 0uctk --gas-adjustment 1.5 --gas auto -y 

Delegate to yourself

        shentud tx staking delegate $(shentud keys show wallet --bech val -a) 1000000uctk --from wallet --chain-id shentu-2.2 --gas-prices 0uctk --gas-adjustment 1.5 --gas auto -y

Edit Existing Validator

        shentud tx staking edit-validator \
        --new-moniker="Moniker" \
        --chain-id=shentu-2.2 \
        --commission-rate=0.1 \
        --from=wallet \
        --gas-prices=0uctk \
        --gas-adjustment=1.5 \
        --gas=auto \
        -y



        
