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
./network.sh deployCC -ccn Vax-Ledger -ccp ../../Vax-Ledger/Chaincode/ -ccl go -c coldchannel -ccv 2.0 -ccs 2 -cccg ../../Vax-Ledger/Chaincode/collections.json

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
### Setting the transient data
```
export MANUFACTURER=$(echo -n "Pfizer" | base64)
export VACCINE_TYPE=$(echo -n "mRNA" | base64)
```
```
peer chaincode invoke \
  -o localhost:7050 \
  --ordererTLSHostnameOverride orderer.example.com \
  --tls \
  --cafile $ORDERER_CA \
  -C coldchannel \
  -n Vax-Ledger \
  --peerAddresses localhost:7051 \
  --tlsRootCertFiles $ORG1_PEER_TLSROOTCERT \
  --peerAddresses localhost:9051 \
  --tlsRootCertFiles $ORG2_PEER_TLSROOTCERT \
  -c '{"Args":["VaxContract:CreateBatch","Batch-01","12-01-2025","12-01-2027","12.3","15.2","{\"timestamp\":\"07-07-2025T10:00:00Z\",\"temperature\":13.5}","Created"]}' \
  --transient "{\"manufacturer\":\"$MANUFACTURER\",\"vaccineType\":\"$VACCINE_TYPE\"}"

```
### Query
```
peer chaincode query -C coldchannel -n Vax-Ledger -c '{"Args":["VaxContract:ReadBatch","Batch-02"]}'
```
```
peer chaincode query -C coldchannel -n Vax-Ledger -c '{"function":"GetAllBatch","Args":[]}'
```
### Delete Invoke
```
peer chaincode invoke \
  -o localhost:7050 \
  --ordererTLSHostnameOverride orderer.example.com \
  --tls \
  --cafile $ORDERER_CA \
  -C coldchannel \
  -n Vax-Ledger \
  --peerAddresses localhost:7051 \
  --tlsRootCertFiles $ORG1_PEER_TLSROOTCERT \
  -c '{"Args":["VaxContract:DeleteBatch","Batch-01"]}'
```

### Env veriables of Org2
```
export CORE_PEER_LOCALMSPID=Org2MSP

export CORE_PEER_TLS_ROOTCERT_FILE=${PWD}/organizations/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt

export CORE_PEER_MSPCONFIGPATH=${PWD}/organizations/peerOrganizations/org2.example.com/users/Admin@org2.example.com/msp

export CORE_PEER_ADDRESS=localhost:9051


```

### Query Temp History
```
peer chaincode query   -C coldchannel   -n Vax-Ledger   -c '{"Args":["TempContract:GetTemperatureLogHistory", "Batch-01"]}'
```
### query of read temp for org2 and org3
```

```
### org3 env var
```
export CORE_PEER_LOCALMSPID=Org3MSP

export CORE_PEER_TLS_ROOTCERT_FILE=${PWD}/organizations/peerOrganizations/org3.example.com/peers/peer0.org3.example.com/tls/ca.crt

export CORE_PEER_MSPCONFIGPATH=${PWD}/organizations/peerOrganizations/org3.example.com/users/Admin@org3.example.com/msp

export CORE_PEER_ADDRESS=localhost:11051
```
### invoke for templog org3
```
peer chaincode invoke -o localhost:7050 --ordererTLSHostnameOverride orderer.example.com --tls --cafile $ORDERER_CA -C coldchannel -n Vax-Ledger --peerAddresses localhost:7051 --tlsRootCertFiles $ORG1_PEER_TLSROOTCERT --peerAddresses localhost:9051 --tlsRootCertFiles $ORG2_PEER_TLSROOTCERT -c '{"function":"AddTemperatureLog","Args":["Batch-01", "5.3", "07-07-2025T15:35:00Z"]}'

```

### Query Temp History org3


