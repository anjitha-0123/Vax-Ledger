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
export FABRIC_CFG_PATH=$PWD/../config/

export ORDERER_CA=${PWD}/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem

export ORG1_PEER_TLSROOTCERT=${PWD}/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt

export ORG2_PEER_TLSROOTCERT=${PWD}/organizations/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt

export CORE_PEER_TLS_ENABLED=true
```
### org1 env variables
```
export CORE_PEER_LOCALMSPID=Org1MSP

export CORE_PEER_TLS_ROOTCERT_FILE=${PWD}/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt

export CORE_PEER_MSPCONFIGPATH=${PWD}/organizations/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp

export CORE_PEER_ADDRESS=localhost:7051
```
### Pvt Data
```
cat <<EOF > vaccine.json
{
  "batchID": "Batch-02",
  "manufacturer": "$(echo $MANUFACTURER | base64 --decode)",
  "vaccineType": "$(echo $VACCINE_TYPE | base64 --decode)",
  "manufactureDate": "$(echo $MFG_DATE | base64 --decode)",
  "expiryDate": "$(echo $EXP_DATE | base64 --decode)",
  "quantity": $(echo $QUANTITY | base64 --decode),
  "minTemp": $(echo $MIN_TEMP | base64 --decode),
  "maxTemp": $(echo $MAX_TEMP | base64 --decode)
}
EOF

```
```
export VACCINE_JSON=$(cat vaccine.json | base64 | tr -d '\n')

```
```
peer chaincode invoke -o localhost:7050 --ordererTLSHostnameOverride orderer.example.com --tls --cafile $ORDERER_CA -C coldchannel -n Vax-Ledger --peerAddresses localhost:7051 --tlsRootCertFiles $ORG1_PEER_TLSROOTCERT --peerAddresses localhost:9051 --tlsRootCertFiles $ORG2_PEER_TLSROOTCERT -c '{"Args":["VaxContract:CreateBatch"]}' --transient "{\"vaccine\":\"$VACCINE_JSON\"}"

```


### Query
```
peer chaincode query -C coldchannel -n Vax-Ledger -c '{"Function":"ReadBatch","Args":["Batch-03"]}'
```
```
peer chaincode query -C coldchannel -n Vax-Ledger -c '{"function":"GetAllBatch","Args":[]}'
```
### Deliverbatch invoke Org1
```
peer chaincode invoke \
  -o localhost:7050 \
  --ordererTLSHostnameOverride orderer.example.com \
  --tls \
  --cafile "${PWD}/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem" \
  -C coldchannel \
  -n Vax-Ledger \
  --peerAddresses localhost:7051 \
  --tlsRootCertFiles "${PWD}/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt" \
  -c '{"function":"DeliverBatch","Args":["Batch-02"]}'

```

### Env veriables of Org2
```
export CORE_PEER_LOCALMSPID=Org2MSP

export CORE_PEER_TLS_ROOTCERT_FILE=${PWD}/organizations/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt

export CORE_PEER_MSPCONFIGPATH=${PWD}/organizations/peerOrganizations/org2.example.com/users/Admin@org2.example.com/msp

export CORE_PEER_ADDRESS=localhost:9051


```

### Reciving invoke Org2
```
peer chaincode invoke -o localhost:7050 --ordererTLSHostnameOverride orderer.example.com --tls \
--cafile "${PWD}/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem" \
-C coldchannel -n Vax-Ledger -c '{"function":"ReceiveFromOrg1","Args":["Batch-02"]}'

```
### templog invoke
```
peer chaincode invoke \
  -o localhost:7050 \
  --ordererTLSHostnameOverride orderer.example.com \
  --tls \
  --cafile "${PWD}/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem" \
  -C coldchannel \
  -n Vax-Ledger \
  --peerAddresses localhost:9051 \
  --tlsRootCertFiles "${PWD}/organizations/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt" \
  -c '{"function":"AddTemperatureLog","Args":["Batch-02", "4.5"]}'
```
### query of read temp
```
peer chaincode query \
  -C coldchannel \
  -n Vax-Ledger \
  -c '{"function":"ReadTemperatureLog","Args":["Batch-02"]}'

```


