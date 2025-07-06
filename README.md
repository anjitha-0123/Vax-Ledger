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
./network.sh deployCC -ccn Vax-Ledger -ccp ../../Vax-Ledger/Chaincode/ -ccl go -c coldchannel 
```

## To Down the Network
```
./network.sh down
```
```
`peer chaincode invoke -o localhost:7050 --ordererTLSHostnameOverride orderer.example.com --tls --cafile "${PWD}/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem" -C coldchannel -n Vax-Ledger --peerAddresses localhost:7051 --tlsRootCertFiles "${PWD}/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt" --peerAddresses localhost:9051 --tlsRootCertFiles "${PWD}/organizations/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt" -c '{"function":"CreateBatch","Args":["batch-02","Apolo","Covid","23-01-2025","23-01-2027","150","2.4","5.5"]}'`

```
