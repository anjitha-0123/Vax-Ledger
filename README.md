# Vax-Ledger
A Cold Chain Ledger to ensure the environmental integrity of vaccines using Hyper Ledger Fabric Blockchain platform


## To Build the Network
```
cd fabric-samples/test-network
```
```
./network.sh up createChannel -c coldchannel -ca -s couchdb
```
### Adding Org3
```
cd addOrg3
```
```
./addOrg3.sh up -c coldchannel -ca -s couchdb
```
```
cd ..
```
## To deploy the ChainCode
```
./network.sh deployCC -ccn Vax-Ledger -ccp ../../Vax-Ledger/Chaincode/ -ccl go -c coldchannel -cccg ../../Vax-Ledger/Chaincode/collections.json
```
```
./network.sh deployCC -ccn Vax-Ledger -ccp ../../Vax-Ledger/Chaincode/ -ccl go -c coldchannel -ccv 3.0 -ccs 3 -cccg ../../Vax-Ledger/Chaincode/collections.json

```

## To Down the Network
```
./network.sh down
```

### Invoke
###  general variables
```
```
### org1 env variables
```
```
### Pvt Data
```
export MANUFACTURER=$(echo -n "Pfizer" | base64 | tr -d '\n')
export VACCINE_TYPE=$(echo -n "COVID-19" | base64 | tr -d '\n')
export MFG_DATE=$(echo -n "2025-07-01" | base64 | tr -d '\n')
export EXP_DATE=$(echo -n "2026-07-01" | base64 | tr -d '\n')
export QUANTITY=$(echo -n "1000" | base64 | tr -d '\n')
export MIN_TEMP=$(echo -n "2.0" | base64 | tr -d '\n')
export MAX_TEMP=$(echo -n "8.0" | base64 | tr -d '\n')
```
```
peer chaincode invoke -o localhost:7050 --ordererTLSHostnameOverride orderer.example.com --tls --cafile $ORDERER_CA -C coldchannel -n Vax-Ledger --peerAddresses localhost:7051 --tlsRootCertFiles $ORG1_PEER_TLSROOTCERT --peerAddresses localhost:9051 --tlsRootCertFiles $ORG2_PEER_TLSROOTCERT -c '{"Args":["VaxContract:CreateBatch","Batch-01"]}' --transient "{\"manufacturer\":\"$MANUFACTURER\",\"vaccineType\":\"$VACCINE_TYPE\",\"manufactureDate\":\"$MFG_DATE\",\"expiryDate\":\"$EXP_DATE\",\"quantity\":\"$QUANTITY\",\"minTemp\":\"$MIN_TEMP\",\"maxTemp\":\"$MAX_TEMP\"}"
```


### Query
```
peer chaincode query -C coldchannel -n Vax-Ledger -c '{"function":"ReadBatch","Args":["batch-02"]}'
```
```
peer chaincode query -C coldchannel -n Vax-Ledger -c '{"function":"GetAllBatch","Args":[]}'
```


