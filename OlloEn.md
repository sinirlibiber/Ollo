&#x20;                                     

![](https://i.hizliresim.com/98sn6te.png )

# Ollo Installation Guide
## Hardware Requirements
Like any Cosmos-SDK chain, its hardware requirements are quite modest.

### Minimum Hardware Requirements
 - 3x CPU; the higher the clock speed, the better
 - 4GB OF RAM
 - 80GB Disk
 - Permanent Internet connection (traffic will be at least 10Mbps during the testnet - at least 100Mbps is expected for production)

### Recommended Hardware Requirements
 - 4x CPU; the higher the clock speed, the better
 - 8GB RAM
 - 200 GB storage (SSD or NVME)
 - Permanent Internet connection (traffic will be at least 10Mbps during the testnet - at least 100Mbps is expected for production)

## Ollo Full Node Installation Steps
### Automatic Installation With a Single Script
You can set up your Ollo fullnode in a few minutes using the automatic script below.
During the script, you will be asked for your node name (NODENAME)!


wget -O OLLO.sh https://raw.githubusercontent.com/sinirlibiber/Ollo/main/ollo-gumusbey && chmod+x OLLO.sh && ./OLLO.sh

### Post-Installation Steps

You should make sure that your validator synchronizes the blocks.
You can use the following command to check the synchronization status.
ollod status 2>&1 | jq.SyncInfo

### Create a Wallet
You can use the command below to create a new wallet. Do not forget to save the reminder (mnemonic).
ollod keys add $OLLO_WALLET

(OPTIONAL) To recover your wallet using reminder (mnemonic):
ollod keys add $OLLO_WALLET --recover

To get the list of available wallets:
ollod keys list

### Save Wallet Information
Add a Wallet Address:
OLLO_WALLET_ADDRESS=$(ollod keys show $OLLO_WALLET -a)
OLLO_VALOPER_ADDRESS=$(ollod keys show $OLLO_WALLET --bech val -a)
echo 'export OLLO_WALLET_ADDRESS='${OLLO_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export OLLO_VALOPER_ADDRESS='${OLLO_VALOPER_ADDRESS} >> $HOME/.bash_profile
source $HOME/.bash_profile


### Create a validator
Before creating a validator, please make sure that you have at least 1 tollo (1 tollo is equal to 1000000 utollo) and that your node is synchronized.

To check your wallet balance:
ollod query bank balances $OLLO_WALLET_ADDRESS
> If you can't see your balance in your wallet, it's likely that your node is still being synchronized. Please wait for the synchronization to finish and then continue.

Creating a Validator:
ollod tx staking create-validator \
  --amount 1999000utollo \
  --from $OLLO_WALLET \
  --commission-max-change-rate "0.01" \
  --commission-max-rate "0.2" \
  --commission-rate "0.07" \
  --min-self-delegation "1" \
  --pubkey$(ollod tendermint show-validator) \
  --moniker $OLLO_NO \
  --chain-id $OLLO_ID \
  --fees 250utollo



## Useful Commands
### Service Management
Check The Logs:
journalctl -fu ollod -o cat

Start The Service:
systemctl start ollod

Stop The Service:
systemctl stop ollod

Restart The Service:
systemctl restart ollod

### Node Information
Synchronization Information:
ollod status 2>&1 | jq.SyncInfo

Validator Information:
ollod status 2>&1 | jq.ValidatorInfo

Node Information:
ollod status 2>&1 | jq.NodeInfo

Displays the Node ID:
ollod tendermint show-node-id

### Wallet Transactions
List Wallets:
ollod keys list

Recover wallet using Mnemonic:
ollod keys add $OLLO_WALLET --recover

Deleting a Wallet:
ollod keys delete $OLLO_WALLET

Wallet Balance Inquiry:
ollod query bank balances $OLLO_WALLET_ADDRESS

Balance Transfer from Wallet to Wallet:
ollod tx bank send $OLLO_WALLET_ADDRESS <TO_WALLET_ADDRESS> 10000000utollo

### Voting
ollod tx gov vote 1 yes --from $OLLO_WALLET --chain-id=$OLLO_ID

### Stakes, Delegations and Prizes
Delegate Operation:
ollod tx staking delegate $OLLO_VALOPER_ADDRESS 10000000utollo--from=$OLLO_WALLET--chain-id=$OLLO_ID --gas=auto --fees 250utollo

Re-transfer the share from the verifier to another verifier:
ollod tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 10000000utollo--from=$OLLO_WALLET --chain-id=$OLLO_ID --gas=auto --fees 250utollo
