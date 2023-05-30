# Install kafka & zookeeper
```bash
sudo yum install java-11-openjdk.x86_64 -y
wget https://archive.apache.org/dist/kafka/2.7.1/kafka_2.13-2.7.1.tgz

tar -xvf kafka_2.13-2.7.1.tgz
mv kafka_2.13-2.7.1 /home/kafka
chmod -R 775 /home/kafka
```
# Open port
```bash
firewall-cmd --add-port=9092/tcp --permanent
firewall-cmd --permanent --add-port=2181/tcp
firewall-cmd --permanent --add-port=2888/tcp
firewall-cmd --permanent --add-port=3888/tcp
firewall-cmd --reload
```
# edit file config
```bash
echo "
listeners=PLAINTEXT://172.16.10.62:9092
advertised.listeners=PLAINTEXT://172.16.10.62:9092
" >> /home/kafka/config/server.properties
```
# setup file service
```bash
vi /etc/systemd/system/kafka.service
vi /etc/systemd/system/zookeeper.service
```
# check status service
```bash
systemctl daemon-reload
systemctl restart zookeeper.service
systemctl restart kafka.service

systemctl enable zookeeper.service
systemctl enable kafka.service

systemctl status zookeeper.service
systemctl status kafka.service
```