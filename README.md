# My Tutorials about devops

[My youtube channel](https://studio.youtube.com/channel/UCmJ3RnxnLnx-ZfnyE6A5jaA/videos)

## create docker network

```
docker network create somenetwork
```

## run elastic search

### create custom elasticsearch image

- change the current directory to the elasticsearch

```
docker build -t elastic .
```

### run docker container using customed elastic search

```
docker run -d --name elasticsearch --net somenetwork -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" elastic
```

## run fluentd

### build the custom fluentd image

- change the current directory to the fluentd folder

```
docker build -t fluentd .
```

### run the docker container on the customed image of fluentd

```
docker run --name fluentd -d --net somenetwork -p 8880:8880 fluentd
```

## run the kibana

### build the custom kibana image

- change the current diretctory to kibana folder

```
docker build -t kibana .
```

### run the docker container using the kibana customed image

```
docker run -d --name kibana --net somenetwork -p 5601:5601 kibana
```

## test the lab

```
curl -X POST -d 'json={"key1":"value1" , "key2":"value2"}' http://localhost:9880/access.log
```

# elasticsearch commands

- list all the indexes

```
curl -X GET 'http://localhost:9200/_cat/indices?v'
```

- list all the docs with the index

```

curl -X GET 'http://localhost:9200/fluentd.access.log/_search'

```

- put new doc

```
curl -XPUT --header 'Content-Type: application/json' http://localhost:9200/fluentd.access.log/_doc/200 -d '{
   "key1" : "value1",
   "key2" : "value2",
   "key3" : "value3"
}'
```

# configure dns on your aws ec2 using ubuntu

## install the needed packages

```
sudo aot-get update -y
sudo apt-get install bind9 bind9utils bind9-doc dnsutils -y
```

## configure /etc/bind/named.conf.options

```
options {
        directory "/var/cache/bind";
        auth-nxdomain no;    # conform to RFC1035
     // listen-on-v6 { any; };
        listen-on port 53 { localhost; 172.31.24.11/32; };
        allow-query { localhost; 172.31.0.0/16; };
        forwarders { 8.8.8.8; };
        recursion yes;
        };
```

## configure your zone

```

zone    "abdelali.com"  {
        type master;
        file    "/etc/bind/db.abdelali.com";
 };

zone "108.168.192.in-addr.arpa" {
     type master;
     file "/etc/bind/reverse.abdelali.com"
}
```

## configure your forward lookup zone db

```

; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     server.abdelali.com. root.server.abdelali.com. (
                              17                ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@        IN     NS      server.abdelali.com.
server   IN     A       192.168.108.140
registry IN     A       192.168.108.145
```

## configure your reverse lookup zone db

```
; BIND data file for local loopback interface
;
$TTL 604800
@ IN SOA server.abdelali.com. root.server.abdelali.com. (
17 ; Serial
604800 ; Refresh
86400 ; Retry
2419200 ; Expire
604800 ) ; Negative Cache TTL
;
@ IN NS server.abdelali.com.
server IN A 192.168.108.140
140 IN PTR server.abdelali.com.
145 IN PTR registry.abdelali.com.
```

## Test your dns

- on the dns client

```
sudo nano /etc/resolv.conf
nameserver 172.31.24.11
search abdelali.com
```

# ubuntu solved issues

```
sudo rm /var/lib/apt/lists/lock
sudo rm /var/lib/apt/lists/lock-frontend
```

```
sudo rm /var/cache/apt/archives/lock
sudo rm /var/lib/dpkg/lock
```

```
sudo dpkg --configure -a
sudo apt install -f
```

```
sudo rm /var/lib/dpkg/updates/0004
sudo dpkg --configure -a
```

# Configure the dhcp server

## install the dhcp server on ubuntu

```
sudo apt-get install isc-dhcp-server
```

## a simple /etc/dhcp/dhcpd.conf

```
sudo nano /etc/dhcp/dhcp.conf
```

### assign a static ip address to the server

```
sudo nano /etc/network/interfaces
```

then

```
auto ens33
iface ens33 inet static
address 172.31.24.120
netmask 255.255.255.0
gateway 172.31.24.1
```

### configure the dhcp server

```
default-lease-time 600;
max-lease-time 7200;
authoritative;

subnet 192.168.108.0 netmask 255.255.255.0 {
 range 192.168.108.100 192.168.108.200;
 option routers 192.168.108.254;
 option domain-name-servers 192.168.108.140;
 option domain-name "abdelali.com";
}
```

### assigning static iP address to a host

- select the MAC Address of the interface from which you’re planning to connect to the network

* the image to see how to obtain your server mac address

<img src="https://raw.githubusercontent.com/Abdelali12-codes/kubernetes-jenkins-ansible-dns-privateregistry-vpc-peering-connection/master/macaddress.png" >

```
host archmachine {
hardware ethernet 00:0c:29:08:3c:cc;
fixed-address 172.31.24.170;
}
```

### bind the dhcp server to an interface

```
sudo nano /etc/default/isc-dhcp-server
```

### check your network configuration

```
ifconfig
```

<img src="https://raw.githubusercontent.com/Abdelali12-codes/kubernetes-jenkins-ansible-dns-privateregistry-vpc-peering-connection/master/interface.png" >

###

- add this line to the file /etc/default/isc-dhcp-server

* replace the eth0 with your interface name in our case is ens33

```
INTERFACESv4="eth0"
INTERFACESv6=""
```

### reset the dhcp server ip address

```
sudo nano /etc/network/interfaces
```

- add the below line to the file /etc/network/interfaces

```
auto ens33
iface ens33 inet static
address 172.31.24.120
netmask 255.255.255.0
gateway 172.31.24.1
```

### restart your dhcp server

```
sudo service isc-dhcp-server restart
```

or

```
sudo systemctl restart isc-dhcp-server.service
```

# configure the dhcp client

- you must to replace eth0 interface with yours

```

auto eth0
iface eth0 inet dhcp

```

# create SSL/TLS certificate for our nginix server

```
sudo mkdir -p docker-registry/certificates
```

```

sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout certificates/abdelali.com.key -out certificates/abdelali.com.crt

```
