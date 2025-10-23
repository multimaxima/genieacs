# GenieACS Docker dengan Zerotier (Host)

## Cara Install
```bash
docker compose -f Genieacs.yaml up -d
```
## Import Parameter
### 1. Copy files ke container
```bash
docker cp ./parameter/ mongo-genieacs:/tmp/
```
### 2. Restore collections
```bash
docker exec mongo-genieacs mongorestore --db genieacs --collection config              --drop /tmp/parameter/config.bson
docker exec mongo-genieacs mongorestore --db genieacs --collection virtualParameters   --drop /tmp/parameter/virtualParameters.bson
docker exec mongo-genieacs mongorestore --db genieacs --collection presets             --drop /tmp/parameter/presets.bson
docker exec mongo-genieacs mongorestore --db genieacs --collection provisions          --drop /tmp/parameter/provisions.bson
```
## Install Zerotier
```bash
curl -s https://install.zerotier.com | sudo bash
```

### 1. Cek Status
```bash
zerotier-cli status
```
### 2. Join Network
```bash
zerotier-cli join <Zerotier Network ID>
```

## Konfigurasi Firewall Mikrotik
```bash
/ip firewall filter add chain=forward connection-state=established,related action=accept
/ip firewall filter add chain=forward action=accept protocol=tcp src-address=[IP_ZEROTIER_VPS] in-interface=[NAMA_INTERFACE_ZEROTIER] out-interface=[NAMA_INTERFACE_VLAN] dst-port=58000,7547 comment="ACS -> ONU"
/ip firewall filter add chain=forward action=accept protocol=tcp dst-address=[IP_ZEROTIER_VPS] in-interface=[NAMA_INTERFACE_VLAN] out-interface=[NAMA_INTERFACE_VLAN] src-port=58000,7547 comment="ONU -> ACS replies"
/ip firewall filter add chain=forward action=accept protocol=tcp dst-address=[IP_ZEROTIER_VPS] in-interface=[NAMA_INTERFACE_VLAN] out-interface=[NAMA_INTERFACE_ZEROTIER] dst-port=7547 comment="ONU -> ACS CWMP"
/ip firewall filter add chain=forward in-interface=[NAMA_INTERFACE_ZEROTIER] out-interface=[NAMA_INTERFACE_VLAN] action=accept
```
