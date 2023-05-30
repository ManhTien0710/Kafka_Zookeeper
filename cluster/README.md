# INFO
```bash
- server1: 172.16.10.77
- server2: 172.16.10.78
- server3: 172.16.10.79
```
# install zookeeper
```bash
sudo yum install java-11-openjdk.x86_64 -y
groupadd zookeeper
useradd -g zookeeper -d /home/zookeeper -s /sbin/nologin zookeeper

wget https://archive.apache.org/dist/zookeeper/zookeeper-3.6.1/apache-zookeeper-3.6.1-bin.tar.gz
tar -xvzf apache-zookeeper-3.6.1-bin.tar.gz
*** copy source zookeeper --> /home/zookeeper/
sudo chown -R zookeeper:zookeeper /home/zookeeper/*
```
# open port
```bash
firewall-cmd --permanent --add-port=2181/tcp
firewall-cmd --permanent --add-port=2888/tcp
firewall-cmd --permanent --add-port=3888/tcp
firewall-cmd --reload
```
# setup file config
```bash
vi /home/zookeeper/conf/zoo.cfg
*** change:
server.1=172.16.10.77:2888:3888 --> on server1
server.2=172.16.10.78:2888:3888 --> on server2
server.3=172.16.10.79:2888:3888 --> on server3

vi /usr/lib/systemd/system/zookeeper.service
```
# check status zookeeper
```bash
systemctl daemon-reload
systemctl start zookeeper
systemctl enable zookeeper
systemctl status zookeeper
```
# install kafka
```bash
wget https://downloads.apache.org/kafka/3.3.1/kafka-3.3.1-src.tgz
mkdir -p /home/kafka
*** copy source kafka --> /home/kafka/
chmod -R 775 /home/kafka
```
# open port
```bash
firewall-cmd --add-port=9092/tcp --permanent
firewall-cmd --reload
```
# setup file config
```bash
vi kafka/config/server.properties
***change
broker.id=1 --> on server1 and id=2/3 --> on server 2/3

listeners=LISTENER_LAN://172.16.10.77:9092
advertised.listeners=LISTENER_LAN://172.16.10.77:9092
--> on server 1. and change with IP server2/server3

zookeeper.connect=172.16.10.77:2181 --> change on server1 and change with IP on server2/3

vi /usr/lib/systemd/system/kafka.service
```
# check status kafka
```bash
systemctl restart kafka.service
systemctl status kafka.service
systemctl enable kafka.service
```
# check kafka version
```bash
grep "Kafka version" /usr/local/kafka/logs/*
```

# setup cluster
# zookeeper
```bash
create config id:
- in folder /home/zookeeper/data in server 1 create file myid with content:1
- in folder /home/zookeeper/data in server 2 create file myid with content:2
- in folder /home/zookeeper/data in server 3  create file myid with content:3

vi /home/zookeeper/data/myid
```
# edit file config
```bash
vi /home/zookeeper/conf/zoo.cfg
*** change
server.1=172.16.10.77:2888:3888
server.2=172.16.10.78:2888:3888
server.3=172.16.10.79:2888:3888
--> on 3 server
```
# kafka
```bash
vi kafka/config/server.properties
*** change
zookeeper.connect=172.16.10.77:2181,172.16.10.78:2181,172.16.10.79:2181
--> on 3 server
```
# check status service cluster
```bash
systemctl daemon-reload
systemctl start zookeeper
systemctl status zookeeper

systemctl restart kafka.service
systemctl status kafka.service
systemctl enable kafka.service
```