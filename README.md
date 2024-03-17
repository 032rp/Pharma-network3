# Pharma-network3


/////Open a command terminal with in Network folder, let's call this terminal as host terminal
cd Network/
############## host terminal ##############
------------Start the Fabric-CA server and register the ca admin for each organization—----------------
docker-compose -f docker/docker-compose-ca.yaml up -d
sudo chmod -R 777 organizations/
------------Register and enroll the users for each organization—-----------
chmod +x registerEnroll.sh
./registerEnroll.sh
—-------------Build the infrastructure—-----------------
//Build the docker-compose-pharmanet.yaml in the docker folder
docker-compose -f docker/docker-compose-pharmanet.yaml up -d
-------------Generate the genesis block—-------------------------------
//Build the configtx.yaml file in the config folder
export FABRIC_CFG_PATH=./config
export CHANNEL_NAME=mychannel
configtxgen -profile PharmaOrdererGenesis -outputBlock ./channel-artifacts/${CHANNEL_NAME}.block -channelID $CHANNEL_NAME
------ Create the application channel------
export ORDERER_CA=./organizations/ordererOrganizations/pharma-network.com/orderers/orderer.pharma-network.com/msp/tlscacerts/tlsca.pharma-network.com-cert.pem
export ORDERER_ADMIN_TLS_SIGN_CERT=./organizations/ordererOrganizations/pharma-network.com/orderers/orderer.pharma-network.com/tls/server.crt
export ORDERER_ADMIN_TLS_PRIVATE_KEY=./organizations/ordererOrganizations/pharma-network.com/orderers/orderer.pharma-network.com/tls/server.key
osnadmin channel join --channelID $CHANNEL_NAME --config-block ./channel-artifacts/$CHANNEL_NAME.block -o localhost:7053 --ca-file $ORDERER_CA --client-cert $ORDERER_ADMIN_TLS_SIGN_CERT --client-key $ORDERER_ADMIN_TLS_PRIVATE_KEY
osnadmin channel list -o localhost:7053 --ca-file $ORDERER_CA --client-cert $ORDERER_ADMIN_TLS_SIGN_CERT --client-key $ORDERER_ADMIN_TLS_PRIVATE_KEY

Open another terminal with in Fabric-network folder, let's call this terminal as peer0_Manufacturer terminal.
############## peer0_Manufacturer terminal ##############
// Build the core.yaml in peercfg folder
export FABRIC_CFG_PATH=./peercfg
export CHANNEL_NAME=mychannel
export CORE_PEER_LOCALMSPID=manufacturerMSP
export CORE_PEER_TLS_ENABLED=true
export CORE_PEER_TLS_ROOTCERT_FILE=${PWD}/organizations/peerOrganizations/manufacturer.pharma-network.com/peers/peer0.manufacturer.pharma-network.com/tls/ca.crt
export CORE_PEER_MSPCONFIGPATH=${PWD}/organizations/peerOrganizations/manufacturer.pharma-network.com/users/Admin@manufacturer.pharma-network.com/msp
export CORE_PEER_ADDRESS=localhost:7051
export ORDERER_CA=${PWD}/organizations/ordererOrganizations/pharma-network.com/orderers/orderer.pharma-network.com/msp/tlscacerts/tlsca.pharma-network.com-cert.pem
export MANUFACTURER_PEER_TLSROOTCERT=${PWD}/organizations/peerOrganizations/manufacturer.pharma-network.com/peers/peer0.manufacturer.pharma-network.com/tls/ca.crt
export DISTRIBUTOR_PEER_TLSROOTCERT=${PWD}/organizations/peerOrganizations/distributor.pharma-network.com/peers/peer0.distributor.pharma-network.com/tls/ca.crt
export RETAILER_PEER_TLSROOTCERT=${PWD}/organizations/peerOrganizations/retailer.pharma-network.com/peers/peer0.retailer.pharma-network.com/tls/ca.crt
export CONSUMER_PEER_TLSROOTCERT=${PWD}/organizations/peerOrganizations/consumer.pharma-network.com/peers/peer0.consumer.pharma-network.com/tls/ca.crt
export TRANSPORTER_PEER_TLSROOTCERT=${PWD}/organizations/peerOrganizations/transporter.pharma-network.com/peers/peer0.transporter.pharma-network.com/tls/ca.crt
—---------------Join peer to the channel—-------------
peer channel join -b ./channel-artifacts/$CHANNEL_NAME.block
peer channel list

/////Open another terminal with in Network folder, let's call this terminal as peer0_Distributor terminal.
############## peer0_Distributor terminal ##############
export FABRIC_CFG_PATH=./peercfg
export CHANNEL_NAME=mychannel 
export CORE_PEER_LOCALMSPID=distributorMSP 
export CORE_PEER_TLS_ENABLED=true
export CORE_PEER_ADDRESS=localhost:8051 
export CORE_PEER_TLS_ROOTCERT_FILE=${PWD}/organizations/peerOrganizations/distributor.pharma-network.com/peers/peer0.distributor.pharma-network.com/tls/ca.crt
export CORE_PEER_MSPCONFIGPATH=${PWD}/organizations/peerOrganizations/distributor.pharma-network.com/users/Admin@distributor.pharma-network.com/msp
export ORDERER_CA=${PWD}/organizations/ordererOrganizations/pharma-network.com/orderers/orderer.pharma-network.com/msp/tlscacerts/tlsca.pharma-network.com-cert.pem
export MANUFACTURER_PEER_TLSROOTCERT=${PWD}/organizations/peerOrganizations/manufacturer.pharma-network.com/peers/peer0.manufacturer.pharma-network.com/tls/ca.crt
export DISTRIBUTOR_PEER_TLSROOTCERT=${PWD}/organizations/peerOrganizations/distributor.pharma-network.com/peers/peer0.distributor.pharma-network.com/tls/ca.crt
export RETAILER_PEER_TLSROOTCERT=${PWD}/organizations/peerOrganizations/retailer.pharma-network.com/peers/peer0.retailer.pharma-network.com/tls/ca.crt
export CONSUMER_PEER_TLSROOTCERT=${PWD}/organizations/peerOrganizations/consumer.pharma-network.com/peers/peer0.consumer.pharma-network.com/tls/ca.crt
export TRANSPORTER_PEER_TLSROOTCERT=${PWD}/organizations/peerOrganizations/transporter.pharma-network.com/peers/peer0.transporter.pharma-network.com/tls/ca.crt
—---------------Join peer to the channel—-------------
peer channel join -b ./channel-artifacts/$CHANNEL_NAME.block
peer channel list
//Open another terminal with in Network folder, let's call this terminal as peer0_Retailer terminal.
############## peer0_Retailer terminal ##############

export FABRIC_CFG_PATH=./peercfg
export CHANNEL_NAME=mychannel 
export CORE_PEER_LOCALMSPID=retailerMSP
export CORE_PEER_TLS_ENABLED=true
 export CORE_PEER_ADDRESS=localhost:9051 
export CORE_PEER_TLS_ROOTCERT_FILE=${PWD}/organizations/peerOrganizations/retailer.pharma-network.com/peers/peer0.retailer.pharma-network.com/tls/ca.crt
export CORE_PEER_MSPCONFIGPATH=${PWD}/organizations/peerOrganizations/retailer.pharma-network.com/users/Admin@retailer.pharma-network.com/msp
export ORDERER_CA=${PWD}/organizations/ordererOrganizations/pharma-network.com/orderers/orderer.pharma-network.com/msp/tlscacerts/tlsca.pharma-network.com-cert.pem
export MANUFACTURER_PEER_TLSROOTCERT=${PWD}/organizations/peerOrganizations/manufacturer.pharma-network.com/peers/peer0.manufacturer.pharma-network.com/tls/ca.crt
export DISTRIBUTOR_PEER_TLSROOTCERT=${PWD}/organizations/peerOrganizations/distributor.pharma-network.com/peers/peer0.distributor.pharma-network.com/tls/ca.crt
export RETAILER_PEER_TLSROOTCERT=${PWD}/organizations/peerOrganizations/retailer.pharma-network.com/peers/peer0.retailer.pharma-network.com/tls/ca.crt
export CONSUMER_PEER_TLSROOTCERT=${PWD}/organizations/peerOrganizations/consumer.pharma-network.com/peers/peer0.consumer.pharma-network.com/tls/ca.crt
export TRANSPORTER_PEER_TLSROOTCERT=${PWD}/organizations/peerOrganizations/transporter.pharma-network.com/peers/peer0.transporter.pharma-network.com/tls/ca.crt
—---------------Join peer to the channel—-------------
peer channel join -b ./channel-artifacts/$CHANNEL_NAME.block
peer channel list

//Open another terminal with in Network folder, let's call this terminal as peer0_Consumer terminal.
############## peer0_Consumer terminal ##############

export FABRIC_CFG_PATH=./peercfg
export CHANNEL_NAME=mychannel 
export CORE_PEER_LOCALMSPID=consumerMSP 
export CORE_PEER_TLS_ENABLED=true
export CORE_PEER_ADDRESS=localhost:10051 
export CORE_PEER_TLS_ROOTCERT_FILE=${PWD}/organizations/peerOrganizations/consumer.pharma-network.com/peers/peer0.consumer.pharma-network.com/tls/ca.crt
export CORE_PEER_MSPCONFIGPATH=${PWD}/organizations/peerOrganizations/consumer.pharma-network.com/users/Admin@consumer.pharma-network.com/msp
export ORDERER_CA=${PWD}/organizations/ordererOrganizations/pharma-network.com/orderers/orderer.pharma-network.com/msp/tlscacerts/tlsca.pharma-network.com-cert.pem
export MANUFACTURER_PEER_TLSROOTCERT=${PWD}/organizations/peerOrganizations/manufacturer.pharma-network.com/peers/peer0.manufacturer.pharma-network.com/tls/ca.crt
export DISTRIBUTOR_PEER_TLSROOTCERT=${PWD}/organizations/peerOrganizations/distributor.pharma-network.com/peers/peer0.distributor.pharma-network.com/tls/ca.crt
export RETAILER_PEER_TLSROOTCERT=${PWD}/organizations/peerOrganizations/retailer.pharma-network.com/peers/peer0.retailer.pharma-network.com/tls/ca.crt
export CONSUMER_PEER_TLSROOTCERT=${PWD}/organizations/peerOrganizations/consumer.pharma-network.com/peers/peer0.consumer.pharma-network.com/tls/ca.crt
export TRANSPORTER_PEER_TLSROOTCERT=${PWD}/organizations/peerOrganizations/transporter.pharma-network.com/peers/peer0.transporter.pharma-network.com/tls/ca.crt

—---------------Join peer to the channel—-------------

peer channel join -b ./channel-artifacts/$CHANNEL_NAME.block
peer channel list
//Open another terminal with in Network folder, let's call this terminal as peer0_Transporter terminal.
############## peer0_Transporter terminal ##############

export FABRIC_CFG_PATH=./peercfg
export CHANNEL_NAME=mychannel 
export CORE_PEER_LOCALMSPID=transporterMSP 
export CORE_PEER_TLS_ENABLED=true
export CORE_PEER_ADDRESS=localhost:11051 
export CORE_PEER_TLS_ROOTCERT_FILE=${PWD}/organizations/peerOrganizations/transporter.pharma-network.com/peers/peer0.transporter.pharma-network.com/tls/ca.crt
export CORE_PEER_MSPCONFIGPATH=${PWD}/organizations/peerOrganizations/transporter.pharma-network.com/users/Admin@transporter.pharma-network.com/msp
export ORDERER_CA=${PWD}/organizations/ordererOrganizations/pharma-network.com/orderers/orderer.pharma-network.com/msp/tlscacerts/tlsca.pharma-network.com-cert.pem
export MANUFACTURER_PEER_TLSROOTCERT=${PWD}/organizations/peerOrganizations/manufacturer.pharma-network.com/peers/peer0.manufacturer.pharma-network.com/tls/ca.crt
export DISTRIBUTOR_PEER_TLSROOTCERT=${PWD}/organizations/peerOrganizations/distributor.pharma-network.com/peers/peer0.distributor.pharma-network.com/tls/ca.crt
export RETAILER_PEER_TLSROOTCERT=${PWD}/organizations/peerOrganizations/retailer.pharma-network.com/peers/peer0.retailer.pharma-network.com/tls/ca.crt
export CONSUMER_PEER_TLSROOTCERT=${PWD}/organizations/peerOrganizations/consumer.pharma-network.com/peers/peer0.consumer.pharma-network.com/tls/ca.crt
export TRANSPORTER_PEER_TLSROOTCERT=${PWD}/organizations/peerOrganizations/transporter.pharma-network.com/peers/peer0.transporter.pharma-network.com/tls/ca.crt
—---------------Join peer to the channel—-------------
peer channel join -b ./channel-artifacts/$CHANNEL_NAME.block
peer channel list


—-------------Anchor peer update—-----------
############## peer0_Manufacturer terminal ##############
peer channel fetch config ${PWD}/channel-artifacts/config_block.pb -o localhost:7050 --ordererTLSHostnameOverride orderer.pharma-network.com -c $CHANNEL_NAME --tls --cafile $ORDERER_CA
cd channel-artifacts
configtxlator proto_decode --input config_block.pb --type common.Block --output config_block.json
jq '.data.data[0].payload.data.config' config_block.json > config.json
cp config.json config_copy.json
jq '.channel_group.groups.Application.groups.manufacturerMSP.values += {"AnchorPeers":{"mod_policy": "Admins","value":{"anchor_peers": [{"host": "peer0.manufacturer.pharma-network.com","port": 7051}]},"version": "0"}}' config_copy.json > modified_config.json
configtxlator proto_encode --input config.json --type common.Config --output config.pb
configtxlator proto_encode --input modified_config.json --type common.Config --output modified_config.pb
configtxlator compute_update --channel_id ${CHANNEL_NAME} --original config.pb --updated modified_config.pb --output config_update.pb
configtxlator proto_decode --input config_update.pb --type common.ConfigUpdate --output config_update.json
echo '{"payload":{"header":{"channel_header":{"channel_id":"'$CHANNEL_NAME'", "type":2}},"data":{"config_update":'$(cat config_update.json)'}}}' | jq . > config_update_in_envelope.json
configtxlator proto_encode --input config_update_in_envelope.json --type common.Envelope --output config_update_in_envelope.pb
cd ..
peer channel update -f ${PWD}/channel-artifacts/config_update_in_envelope.pb -c $CHANNEL_NAME -o localhost:7050  --ordererTLSHostnameOverride orderer.pharma-network.com --tls --cafile $ORDERER_CA

############## peer0_Distributor terminal ##############
peer channel fetch config ${PWD}/channel-artifacts/config_block.pb -o localhost:7050 --ordererTLSHostnameOverride orderer.pharma-network.com -c $CHANNEL_NAME --tls --cafile $ORDERER_CA
cd channel-artifacts
configtxlator proto_decode --input config_block.pb --type common.Block --output config_block.json
jq '.data.data[0].payload.data.config' config_block.json > config.json
cp config.json config_copy.json
jq '.channel_group.groups.Application.groups.distributorMSP.values += {"AnchorPeers":{"mod_policy": "Admins","value":{"anchor_peers": [{"host": "peer0.distributor.pharma-network.com","port": 8051}]},"version": "0"}}' config_copy.json > modified_config.json

configtxlator proto_encode --input config.json --type common.Config --output config.pb
configtxlator proto_encode --input modified_config.json --type common.Config --output modified_config.pb
configtxlator compute_update --channel_id $CHANNEL_NAME --original config.pb --updated modified_config.pb --output config_update.pb
configtxlator proto_decode --input config_update.pb --type common.ConfigUpdate --output config_update.json
echo '{"payload":{"header":{"channel_header":{"channel_id":"'$CHANNEL_NAME'", "type":2}},"data":{"config_update":'$(cat config_update.json)'}}}' | jq . > config_update_in_envelope.json
configtxlator proto_encode --input config_update_in_envelope.json --type common.Envelope --output config_update_in_envelope.pb
cd ..
peer channel update -f ${PWD}/channel-artifacts/config_update_in_envelope.pb -c $CHANNEL_NAME -o localhost:7050  --ordererTLSHostnameOverride orderer.pharma-network.com --tls --cafile $ORDERER_CA
peer channel getinfo -c $CHANNEL_NAME

############## peer0_Retailer terminal ##############
peer channel fetch config ${PWD}/channel-artifacts/config_block.pb -o localhost:7050 --ordererTLSHostnameOverride orderer.pharma-network.com -c $CHANNEL_NAME --tls --cafile $ORDERER_CA
cd channel-artifacts
configtxlator proto_decode --input config_block.pb --type common.Block --output config_block.json
jq '.data.data[0].payload.data.config' config_block.json > config.json
cp config.json config_copy.json
jq '.channel_group.groups.Application.groups.retailerMSP.values += {"AnchorPeers":{"mod_policy": "Admins","value":{"anchor_peers": [{"host": "peer0.retailer.pharma-network.com","port": 9051}]},"version": "0"}}' config_copy.json > modified_config.json
configtxlator proto_encode --input config.json --type common.Config --output config.pb
configtxlator proto_encode --input modified_config.json --type common.Config --output modified_config.pb
configtxlator compute_update --channel_id $CHANNEL_NAME --original config.pb --updated modified_config.pb --output config_update.pb
configtxlator proto_decode --input config_update.pb --type common.ConfigUpdate --output config_update.json
echo '{"payload":{"header":{"channel_header":{"channel_id":"'$CHANNEL_NAME'", "type":2}},"data":{"config_update":'$(cat config_update.json)'}}}' | jq . > config_update_in_envelope.json
configtxlator proto_encode --input config_update_in_envelope.json --type common.Envelope --output config_update_in_envelope.pb
cd ..
peer channel update -f ${PWD}/channel-artifacts/config_update_in_envelope.pb -c $CHANNEL_NAME -o localhost:7050  --ordererTLSHostnameOverride orderer.pharma-network.com --tls --cafile $ORDERER_CA

############## peer0_Consumer terminal ##############
peer channel fetch config ${PWD}/channel-artifacts/config_block.pb -o localhost:7050 --ordererTLSHostnameOverride orderer.pharma-network.com -c $CHANNEL_NAME --tls --cafile $ORDERER_CA
cd channel-artifacts
configtxlator proto_decode --input config_block.pb --type common.Block --output config_block.json
jq '.data.data[0].payload.data.config' config_block.json > config.json
cp config.json config_copy.json
jq '.channel_group.groups.Application.groups.consumerMSP.values += {"AnchorPeers":{"mod_policy": "Admins","value":{"anchor_peers": [{"host": "peer0.consumer.pharma-network.com","port": 10051}]},"version": "0"}}' config_copy.json > modified_config.json
configtxlator proto_encode --input config.json --type common.Config --output config.pb
configtxlator proto_encode --input modified_config.json --type common.Config --output modified_config.pb
configtxlator compute_update --channel_id $CHANNEL_NAME --original config.pb --updated modified_config.pb --output config_update.pb
configtxlator proto_decode --input config_update.pb --type common.ConfigUpdate --output config_update.json
echo '{"payload":{"header":{"channel_header":{"channel_id":"'$CHANNEL_NAME'", "type":2}},"data":{"config_update":'$(cat config_update.json)'}}}' | jq . > config_update_in_envelope.json
configtxlator proto_encode --input config_update_in_envelope.json --type common.Envelope --output config_update_in_envelope.pb
cd ..
peer channel update -f ${PWD}/channel-artifacts/config_update_in_envelope.pb -c $CHANNEL_NAME -o localhost:7050  --ordererTLSHostnameOverride orderer.pharma-network.com --tls --cafile $ORDERER_CA
############## peer0_Transporter terminal ##############
peer channel fetch config ${PWD}/channel-artifacts/config_block.pb -o localhost:7050 --ordererTLSHostnameOverride orderer.pharma-network.com -c $CHANNEL_NAME --tls --cafile $ORDERER_CA
cd channel-artifacts
configtxlator proto_decode --input config_block.pb --type common.Block --output config_block.json
jq '.data.data[0].payload.data.config' config_block.json > config.json
cp config.json config_copy.json
jq '.channel_group.groups.Application.groups.transporterMSP.values += {"AnchorPeers":{"mod_policy": "Admins","value":{"anchor_peers": [{"host": "peer0.transporter.pharma-network.com","port": 11051}]},"version": "0"}}' config_copy.json > modified_config.json
configtxlator proto_encode --input config.json --type common.Config --output config.pb
configtxlator proto_encode --input modified_config.json --type common.Config --output modified_config.pb
configtxlator compute_update --channel_id $CHANNEL_NAME --original config.pb --updated modified_config.pb --output config_update.pb
configtxlator proto_decode --input config_update.pb --type common.ConfigUpdate --output config_update.json
echo '{"payload":{"header":{"channel_header":{"channel_id":"'$CHANNEL_NAME'", "type":2}},"data":{"config_update":'$(cat config_update.json)'}}}' | jq . > config_update_in_envelope.json
configtxlator proto_encode --input config_update_in_envelope.json --type common.Envelope --output config_update_in_envelope.pb
cd ..
peer channel update -f ${PWD}/channel-artifacts/config_update_in_envelope.pb -c $CHANNEL_NAME -o localhost:7050  --ordererTLSHostnameOverride orderer.pharma-network.com --tls --cafile $ORDERER_CA
peer channel getinfo -c $CHANNEL_NAME

—-----------------Chaincode lifecycle—-------------------

***Build the chaincode (Change MSPId and collection file)

***Make sure that KBA-Automobile chaincode is available in Chaincode folder which is at the same level of Automobile-network.

**************** peer0_Manufacturer terminal ******************
peer lifecycle chaincode package pharmanet.tar.gz --path ${PWD}/../chaincode/ --lang node --label pharmanet_1.0
peer lifecycle chaincode install pharmanet.tar.gz
peer lifecycle chaincode queryinstalled
export CC_PACKAGE_ID=$(peer lifecycle chaincode calculatepackageid pharmanet.tar.gz)
**************** peer0_Distributor terminal ******************
peer lifecycle chaincode install pharmanet.tar.gz
export CC_PACKAGE_ID=$(peer lifecycle chaincode calculatepackageid pharmanet.tar.gz)
**************** peer0_Retailer terminal ******************
peer lifecycle chaincode install pharmanet.tar.gz
export CC_PACKAGE_ID=$(peer lifecycle chaincode calculatepackageid pharmanet.tar.gz)
**************** peer0_Consumer terminal ******************
peer lifecycle chaincode install pharmanet.tar.gz
export CC_PACKAGE_ID=$(peer lifecycle chaincode calculatepackageid pharmanet.tar.gz)
**************** peer0_Transporter terminal ******************
peer lifecycle chaincode install pharmanet.tar.gz
export CC_PACKAGE_ID=$(peer lifecycle chaincode calculatepackageid pharmanet.tar.gz)

################## Approve the chaincode#########################
**************** peer0_Manufacturer terminal ******************
peer lifecycle chaincode approveformyorg -o localhost:7050 --ordererTLSHostnameOverride orderer.pharma-network.com --channelID $CHANNEL_NAME --name PharmaNetwork --version 1.0  --package-id $CC_PACKAGE_ID --sequence 1 --tls --cafile $ORDERER_CA –waitForEvent
**************** peer0_Distributor terminal ******************
peer lifecycle chaincode approveformyorg -o localhost:7050 --ordererTLSHostnameOverride orderer.pharma-network.com --channelID $CHANNEL_NAME --name PharmaNetwork --version 1.0  --package-id $CC_PACKAGE_ID --sequence 1 --tls --cafile $ORDERER_CA –waitForEvent
**************** peer0_Retailer terminal ******************
peer lifecycle chaincode approveformyorg -o localhost:7050 --ordererTLSHostnameOverride orderer.pharma-network.com --channelID $CHANNEL_NAME --name PharmaNetwork --version 1.0  --package-id $CC_PACKAGE_ID --sequence 1 --tls --cafile $ORDERER_CA –waitForEvent
**************** peer0_Consumer terminal ******************
peer lifecycle chaincode approveformyorg -o localhost:7050 --ordererTLSHostnameOverride orderer.pharma-network.com --channelID $CHANNEL_NAME --name PharmaNetwork --version 1.0  --package-id $CC_PACKAGE_ID --sequence 1 --tls --cafile $ORDERER_CA –waitForEvent

**************** peer0_Transporter terminal ******************
peer lifecycle chaincode approveformyorg -o localhost:7050 --ordererTLSHostnameOverride orderer.pharma-network.com --channelID $CHANNEL_NAME --name PharmaNetwork --version 1.0  --package-id $CC_PACKAGE_ID --sequence 1 --tls --cafile $ORDERER_CA –waitForEvent
################# Commit the Chaincode ###################

**************** peer0_Manufacturer terminal ******************
peer lifecycle chaincode checkcommitreadiness --channelID $CHANNEL_NAME --name PharmaNetwork --version 1.0 --sequence 1  --tls --cafile $ORDERER_CA --output json
peer lifecycle chaincode commit -o localhost:7050 --ordererTLSHostnameOverride orderer.pharma-network.com --channelID $CHANNEL_NAME --name PharmaNetwork --version 1.0 --sequence 1  --tls --cafile $ORDERER_CA --peerAddresses localhost:7051 --tlsRootCertFiles $MANUFACTURER_PEER_TLSROOTCERT --peerAddresses localhost:8051 --tlsRootCertFiles $DISTRIBUTOR_PEER_TLSROOTCERT --peerAddresses localhost:9051 --tlsRootCertFiles $RETAILER_PEER_TLSROOTCERT --peerAddresses localhost:10051 --tlsRootCertFiles $CONSUMER_PEER_TLSROOTCERT --peerAddresses localhost:11051 --tlsRootCertFiles $TRANSPORTER_PEER_TLSROOTCERT
peer lifecycle chaincode querycommitted --channelID $CHANNEL_NAME --name PharmaNetwork --cafile $ORDERER_CA
################## Lets Invoke and Query the Chaincode ###########################

peer chaincode invoke -o localhost:7050 --ordererTLSHostnameOverride orderer.auto.com --tls --cafile $ORDERER_CA -C $CHANNEL_NAME -n KBA-Automobile --peerAddresses localhost:7051 --tlsRootCertFiles $MANUFACTURER_PEER_TLSROOTCERT --peerAddresses localhost:9051 --tlsRootCertFiles $DEALER_PEER_TLSROOTCERT --peerAddresses localhost:11051 --tlsRootCertFiles $MVD_PEER_TLSROOTCERT -c '{"function":"CreateCar","Args":["Car-11", "Tata", "Nexon", "White", "Factory-1", "22/07/2023"]}'



peer chaincode query -C $CHANNEL_NAME -n KBA-Automobile -c '{"Args":["GetAllCars"]}'




--------Invoke Private Transaction----------

**************** peer0_Dealer terminal ******************

export MAKE=$(echo -n "Tata" | base64 | tr -d \\n)

export MODEL=$(echo -n "Tiago" | base64 | tr -d \\n)

export COLOR=$(echo -n "White" | base64 | tr -d \\n)

export DEALER_NAME=$(echo -n "XXX" | base64 | tr -d \\n)



peer chaincode invoke -o localhost:7050 --ordererTLSHostnameOverride orderer.auto.com --tls --cafile $ORDERER_CA -C $CHANNEL_NAME -n KBA-Automobile --peerAddresses localhost:7051 --tlsRootCertFiles $MANUFACTURER_PEER_TLSROOTCERT --peerAddresses localhost:9051 --tlsRootCertFiles $DEALER_PEER_TLSROOTCERT --peerAddresses localhost:11051 --tlsRootCertFiles $MVD_PEER_TLSROOTCERT -c '{"Args":["OrderContract:CreateOrder","ORD201"]}' --transient "{\"make\":\"$MAKE\",\"model\":\"$MODEL\",\"color\":\"$COLOR\",\"dealerName\":\"$DEALER_NAME\"}"



peer chaincode query -C $CHANNEL_NAME -n KBA-Automobile -c '{"Args":["OrderContract:ReadOrder","ORD201"]}'



///Stop the network
############## host terminal ##############

docker-compose -f docker/docker-compose-2org.yaml down
docker-compose -f docker/docker-compose-ca.yaml down
docker volume rm $(docker volume ls -q)
sudo rm -rf channel-artifacts/
sudo rm -rf organizations/
docker ps -a

// if there still exists the containers then execute the following commands.

docker rm $(docker container ls -q) --force
docker container prune
docker system prune
docker volume prune
docker network prune
