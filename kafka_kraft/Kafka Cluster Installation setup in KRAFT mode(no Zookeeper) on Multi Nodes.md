********Kafka Cluster multi different node install/setup (in Kraft mode, no zookeeper)*********Kafka V3.4.0======

1. Login (ssh) to each box update & install yum & wget login to jump box or connect to machines that you have access directly
```
yum update -y
yum install -y wget curl java
``` 
3. AWS EC2 Instances(RHEL): add into /etc/hosts file and ping the hosts from one box to other, it should communicate each other.
```
10.30.9.31 kafka1
10.30.9.32 kafka2
10.30.9.33 kafka3

echo "10.30.9.31 kafka1" >> /etc/hosts
echo "10.30.9.32 kafka2" >> /etc/hosts
echo "10.30.9.33 kafka3" >> /etc/hosts
```
3. Create a user "kafka"
```
useradd kafka
```
4. Create couple of directories and update & install yum & wget
```
mkdir -p /data/kafka/kafka1.logs /data/kafka/kafka2.logs /data/kafka/kafka3.logs
mkdir -p /opt/kafka
chown -R kafka:kafka data opt
```
4. Download kafka (3.4.0, kraft) and untar - reference https://kafka.apache.org/quickstart
```
cd /opt/kafka
wget https://dlcdn.apache.org/kafka/3.9.0/kafka_2.12-3.9.0.tgz
tar -xzf kafka_2.12-3.9.0.tgz
```
5. Actual fun begins here,...at "server.properties" file ** key is kraft folder

cd /opt/kafka/kafka_2.12-3.9.0/config/kraft
do below changes in "server.properties" on each box 
```
5.1 process.roles=broker,controller --> its optional change by default both roles => trên các node
5.2 node.id=1 (node.id=2,node.id=3,...node.id=n based on N no.of nodes on respective node)
5.3 controller.quorum.voters=1@kafka1:9093,2@kafka2:9093,3@kafka3:9093 (list of voters to elect leader) => trên các node
5.4 listeners
listeners=PLAINTEXT://kafka1:9092,CONTROLLER://kafka1:9093 -->kafka1 box
listeners=PLAINTEXT://kafka2:9092,CONTROLLER://kafka2:9093 -->kafka2 box
listeners=PLAINTEXT://kafka3:9092,CONTROLLER://kafka3:9093 -->kafka3 box
5.5 advertised	
advertised.listeners=PLAINTEXT://kafka1:9092  -->kafka1 box
advertised.listeners=PLAINTEXT://kafka2:9092  -->kafka2 box
advertised.listeners=PLAINTEXT://kafka3:9092  -->kafka3 box
5.6 log.dirs
log.dirs=/data/kafka/kafka1.logs  -->Kafka1 box
log.dirs=/data/kafka/kafka2.logs  -->Kafka2 box
log.dirs=/data/kafka/kafka3.logs  -->Kafka3 box
```
6. Generate a Cluster UUID in anyone of the box, and make sure rest of other boxes must have same cluster ID by export
```
KAFKA_CLUSTER_ID="$(/opt/kafka/bin/kafka-storage.sh random-uuid)" --> any box
and set/export same echo $KAFKA_CLUSTER_ID in other boxes
export KAFKA_CLUSTER_ID=`echo $KAFKA_CLUSTER_ID` (backquote `, not single ' or double ") 
or copy & past export KAFKA_CLUSTER_ID="bV7J9kM5Q8eroI4E_FtpSw" => chạy trên 2 node còn lại
```
7. Format Log Directories
```
/opt/kafka/bin/kafka-storage.sh format -t $KAFKA_CLUSTER_ID -c /opt/kafka/config/kraft/server.properties
```
9. Start the Kafka Server => Bỏ qua thực hiện từ bước 10
```
1. /opt/kafka/bin/kafka-server-start.sh /opt/kafka/config/kraft/server.properties -->interactive mode
2. /opt/kafka/bin/kafka-server-start.sh -daemon /opt/kafka/config/kraft/server.properties -->backend mode
3. Adding service mode, to start & stop on reboot or failure/down automatically - no manual intervention
```
10. Adding Kafka service daemon process, Add below content to the kafka.service file
```
cat << EOF > /etc/systemd/system/kafka.service
[Unit]
Description=kafka Service
After=network-online.target
Requires=network-online.target

[Service]

Type=simple
Restart=on-failure

User=kafka
Group=kafka

ExecStart=/opt/kafka/bin/kafka-server-start.sh /opt/kafka/config/kraft/server.properties
ExecStop=/opt/kafka/bin/kafka-server-stop.sh /opt/kafka/config/kraft/server.properties
WorkingDirectory=/opt/kafka

[Install]
WantedBy=multi-user.target
EOF


cat << EOF > /etc/systemd/system/kafka.service
[Unit]
Description=kafka Service
After=network-online.target
Requires=network-online.target

[Service]

Type=simple
Restart=on-failure

User=kafka
Group=kafka

ExecStart=/opt/kafka/kafka_2.12-3.9.0/bin/kafka-server-start.sh /opt/kafka/kafka_2.12-3.9.0/config/kraft/server.properties
ExecStop=/opt/kafka/kafka_2.12-3.9.0/bin/kafka-server-stop.sh /opt/kafka/kafka_2.12-3.9.0/config/kraft/server.properties
WorkingDirectory=/opt/kafka/kafka_2.12-3.9.0

[Install]
WantedBy=multi-user.target
EOF
```

11. make sure file permissions and load
```
sudo systemctl daemon-reload
systemctl enable kafka.service --> To enbale service to pick-up on reboot
sudo systemctl start kafka
sudo systemctl stop kafka
sudo systemctl status kafka
```


1. Few Samples:
```
sudo /opt/kafka/bin/kafka-topics.sh --bootstrap-server kafka1:9092,kafka2:9092,kafka3:9092 --version
sudo /opt/kafka/bin/kafka-topics.sh --bootstrap-server kafka1:9092,kafka2:9092,kafka3:9092 --list
sudo /opt/kafka/bin/kafka-topics.sh --bootstrap-server kafka1:9092,kafka2:9092,kafka3:9092 --create --topic testsj
sudo /opt/kafka/bin/kafka-topics.sh --bootstrap-server kafka1:9092,kafka2:9092,kafka3:9092 --create --topic testsj-r3p10 --partitions 10 --replication-factor 3
```
