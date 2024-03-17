# Deploy Kafka service using systemd

## Prerequisite

### 1. update package

```
sudo apt update
```

### 2. install java/jdk

```
sudo apt install default-jdk
```

* Check java version
```
java --version
```

## Install & Configuration

### 1\. Add User

- Create name user

```
sudo useradd kafka -m -s /bin/bash
```

- create password for user that we create

```
sudo passwd kafka
```

- add user to sudo group

```
sudo adduser kafka 
```

- login via sudo to user that we create

```
sudo su -l kafka
```

### 2\. Download & Extract kafka binary

- Create directory for kafka installer

```
mkdir ~/Downloads
```

- download kafka using wget

```
wget https://downloads.apache.org/kafka/3.7.0/kafka_2.12-3.7.0.tgz
```

> you can download and see version from this web, copy the link download

```
https://kafka.apache.org/downloads
```

- Create kafka folder and change directory to kafka folder

```
mkdir ~/kafka && cd ~/kafka
```

- Extract kafka tar from download folder to kafka folder (now at kafka folder)

```
tar -xvzf ~/Downloads/kafka.tgz --strip 1
```

### 3. Kafka server configuration, edit file server.properties

- edit file

```
sudo nano ~/kafka/config/server.properties
```

- add this text at below

```
delete.topic.enable = true
```

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
User=kafka
Environment="JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64"
ExecStart=/bin/sh -c '/home/kafka/kafka/bin/kafka-server-start.sh /home/kafka/kafka/config/server.properties > /home/kafka/kafka/kafka.log 2>&1'
ExecStop=/home/kafka/kafka/bin/kafka-server-stop.sh
Restart=on-abnormal
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
ExecStart=/home/kafka/kafka/bin/zookeeper-server-start.sh /home/kafka/kafka/config/zookeeper.properties
ExecStop=/home/kafka/kafka/bin/zookeeper-server-stop.sh
Restart=on-abnormal
[Install]
WantedBy=multi-user.target
```

### 5. Manage kafka Service to start kafka

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

* Create Topic
```
~/kafka/bin/kafka-topics.sh --create --bootstrap-server localhost:9092 --replication-factor 1 --partitions 1 --topic TutorialTopic
```

* Start Topic
```
echo "Hello, World" | ~/kafka/bin/kafka-console-producer.sh --broker-list localhost:9092 --topic TutorialTopic > /dev/null
```

* Display Topic
```
~/kafka/bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic TutorialTopic --from-beginning
```



# Zookeeper Quorum and Kafka Cluster id check

