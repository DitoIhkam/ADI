![alt text](?raw=true)
# Deploy Kafka service using systemd

## Prerequisite

### 1. update package

```
sudo dnf update
```
![alt text](?raw=true)

### 2. install java/jdk

```
sudo dnf install java-17-openjdk java-17-openjdk-devel
```

* Check java version
```
java -version
```
![alt text](?raw=true)

## Install & Configuration

### 1. Add User

- Create name user

```
sudo useradd kafka -m
```

- create password for user that we create

```
sudo passwd kafka
```

- add user to sudo group

```
sudo usermod -aG wheel kafka
```

- login via sudo to user that we create

```
su -l kafka
```
![alt text](?raw=true)


### 2. Download & Extract kafka binary

- Create directory for kafka installer and go to downloads folder

```
mkdir ~/downloads && cd ~/downloads
```

- download kafka using wget

```
wget https://downloads.apache.org/kafka/3.7.0/kafka_2.12-3.7.0.tgz
```
![alt text](?raw=true)


> you can download and see version from this web, copy the link download



```
https://kafka.apache.org/downloads
```

- Go to this path and extract the targz. it will be extract folder

```
cd /usr/local
```

- Extract kafka tar from download folder to kafka folder (now at kafka folder)

```
tar -xvzf ~/downloads/kafka.tgz --strip 1
```
> change kafka.tgz to file kafka name.

![alt text](?raw=true)



### 4: Create unit file Systemd and running kafka Server. Create systemd service for Kafka and ZooKeeper, then run Kafka:

```
sudo nano /etc/systemd/system/kafka.service
```

- Copy this text below to the file kafka.service

```
[Unit]
Description=Apache Kafka Server
Documentation=http://kafka.apache.org/documentation.html
Requires=zookeeper.service

[Service]
Type=simple
Environment="JAVA_HOME=/usr/lib/jvm/jre-17-openjdk"
ExecStart=/usr/bin/bash /usr/local/kafka/bin/kafka-server-start.sh /usr/local/kafka/config/server.properties
ExecStop=/usr/bin/bash /usr/local/kafka/bin/kafka-server-stop.sh

[Install]
WantedBy=multi-user.target
```

```
sudo nano /etc/systemd/system/zookeeper.service
```

- Copy this text below to zookeeper.service

```
[Unit]
Description=Apache Zookeeper server
Documentation=http://zookeeper.apache.org
Requires=network.target remote-fs.target
After=network.target remote-fs.target

[Service]
Type=simple
ExecStart=/usr/bin/bash /usr/local/kafka/bin/zookeeper-server-start.sh /usr/local/kafka/config/zookeeper.properties
ExecStop=/usr/bin/bash /usr/local/kafka/bin/zookeeper-server-stop.sh
Restart=on-abnormal

[Install]
WantedBy=multi-user.target
```

### 5. Manage kafka Service to start kafka

* Reload
```
sudo systemctl daemon-reload
```

* Start Kafka
```
sudo systemctl start kafka
```

* Check status kafka
```
sudo systemctl status kafka
```

* Enable Kafka
```
sudo systemctl enable kafka
```

* Start zookeeper
```
sudo systemctl start zookeeper
```

* Check status zookeeper
```
sudo systemctl status zookeeper
```

* Reload
```
sudo systemctl daemon-reload
```

# Create Topic, Test produce data and consume data using CLI

### 1. Test installation, create kafka topic

Go to /usr/local/bin, and execute this script

* Create Topic
```
./kafka-topics.sh --create --bootstrap-server localhost:9092 --replication-factor 1 --partitions 1 --topic TutorialTopic
```

* Start Topic
```
echo "Hello, World" | ./kafka-console-producer.sh --broker-list localhost:9092 --topic TutorialTopic > /dev/null
```

* Display Topic
```
./kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic TutorialTopic --from-beginning
```



# Zookeeper Quorum and Kafka Cluster id check

* Check topic list
```
~/kafka/bin/kafka-topics.sh --bootstrap-server localhost:9092 --list
```
result
```
TutorialTopic
__consumer_offsets
```

```./kafka-topics.sh --bootstrap-server localhost:9092 --describe --to
pic TutorialTopic
```
result
```
Topic: TutorialTopic    TopicId: HUnxML0SQt-naKfL33C7rA PartitionCount: 1       ReplicationFactor: 1 Configs:
        Topic: TutorialTopic    Partition: 0    Leader: 0       Replicas: 0     Isr: 0
```

```
./bin/kafka-consumer-groups.sh --bootstrap-server localhost:9092 --list
```

result
```
no display
```

```
./bin/kafka-consumer-groups.sh --bootstrap-server localhost:9092 --describe --group mygroup
```

* result
```
no display
```

```
bin/kafka-consumer-groups.sh --bootstrap-server localhost:9092 --describe --group my-group
```

based on web kafka, cluster
