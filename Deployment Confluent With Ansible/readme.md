![alt text](?raw=true)

# Data VM

| Nama VM  | IP | List Deployment |
| ------------- | ------------- |-- |
| Deploy1  | 172.100.10.172  | zookeeper, kafka broker, kafka connect, kafka rest |
| Deploy2  | 172.100.10.173  |zookeeper, kafka broker, schema registry, ksql |
| Deploy3  | 172.100.10.173  | zookeeper, kafka broker, control center |

# Install Ansible

```
sudo dnf update
```
```
sudo dnf install epel-release
```
```
sudo dnf install ansible
```

Cek versi ansible
```
ansible --version
```

![alt text](https://github.com/DitoIhkam/ADI/blob/main/img/ansible%20version.png?raw=true)

# Script

Script hosts serta ansible.cfg ini terletak di /etc/ansible. 
hosts
```
[zookeepers]
10.100.13.172
10.100.13.173
10.100.13.174

[kafka_brokers]
10.100.13.172
10.100.13.173
10.100.13.174

[kafka_connect]
10.100.13.172

[kafka_rest]
10.100.13.172

[schema_registry]
10.100.13.173

[ksql]
10.100.13.173

[control_center]
10.100.13.174


[all:vars]
ansible_user=root
ansible_ssh_private_key_file=~/.ssh/id_rsa
```

![alt text](https://github.com/DitoIhkam/ADI/blob/main/img/hosts.png?raw=true)

ansible.cfg

```
[defaults]
inventory = /etc/ansible/hosts
private_key_file = ~/.ssh/id_rsa
host_key_checking = false
interpreter_python =  auto_silent
```

![alt text](https://github.com/DitoIhkam/ADI/blob/main/img/ansible.cfg.png?raw=true)

test ping apakah koneksi ssh berhasil ke semua vm/ip

```
ansible all -m ping
```

![alt text](https://github.com/DitoIhkam/ADI/blob/main/img/Test%20ping.png?raw=true)

script deployment

```
---
- name: Deploy Confluent Platform
  hosts: all
  become: yes
  tasks:
    - name: Install Java
      yum:
        name: java-1.8.0-openjdk
        state: present

- name: Setup Zookeeper
  hosts: zookeepers
  become: yes
  tasks:
    - name: Download and Extract Zookeeper
      get_url:
        url: https://archive.apache.org/dist/zookeeper/zookeeper-3.6.2/apache-zookeeper-3.6.2-bin.tar.gz
        dest: /opt/zookeeper.tar.gz
    - name: Extract Zookeeper
      unarchive:
        src: /opt/zookeeper.tar.gz
        dest: /opt/
        remote_src: yes
    - name: Create Zookeeper Config
      copy:
        dest: /opt/apache-zookeeper-3.6.2-bin/conf/zoo.cfg
        content: |
          tickTime=2000
          dataDir=/var/lib/zookeeper
          clientPort=2181
          initLimit=5
          syncLimit=2
          server.1=10.100.13.100:2888:3888
          server.2=10.100.13.102:2888:3888
          server.3=10.100.13.103:2888:3888

- name: Setup Kafka Broker
  hosts: kafka_brokers
  become: yes
  tasks:
    - name: Download and Extract Kafka
      get_url:
        url: https://archive.apache.org/dist/kafka/2.8.0/kafka_2.12-2.8.0.tgz
        dest: /opt/kafka.tgz
    - name: Extract Kafka
      unarchive:
        src: /opt/kafka.tgz
        dest: /opt/
        remote_src: yes
    - name: Create Kafka Config
      copy:
        dest: /opt/kafka_2.12-2.8.0/config/server.properties
        content: |
          broker.id={{ ansible_hostname[-1] | int }}
          log.dirs=/var/lib/kafka
          zookeeper.connect=10.100.13.100:2181,10.100.13.102:2181,10.100.13.103:2181

- name: Setup Kafka Connect
  hosts: kafka_connect
  become: yes
  tasks:
    - name: Create Kafka Connect Config
      copy:
        dest: /opt/kafka_2.12-2.8.0/config/connect-distributed.properties
        content: |
          bootstrap.servers=10.100.13.100:9092,10.100.13.102:9092,10.100.13.103:9092
          key.converter=org.apache.kafka.connect.json.JsonConverter
          value.converter=org.apache.kafka.connect.json.JsonConverter

- name: Setup Kafka REST Proxy
  hosts: kafka_rest
  become: yes
  tasks:
    - name: Create Kafka REST Config
      copy:
        dest: /opt/kafka_2.12-2.8.0/config/kafka-rest.properties
        content: |
          bootstrap.servers=10.100.13.100:9092,10.100.13.102:9092,10.100.13.103:9092
          schema.registry.url=http://10.100.13.102:8081

- name: Setup Schema Registry
  hosts: schema_registry
  become: yes
  tasks:
    - name: Create Schema Registry Config
      copy:
        dest: /opt/kafka_2.12-2.8.0/config/schema-registry.properties
        content: |
          kafkastore.bootstrap.servers=10.100.13.100:9092,10.100.13.102:9092,10.100.13.103:9092
          kafkastore.topic=_schemas

- name: Setup KSQL
  hosts: ksql
  become: yes
  tasks:
    - name: Create KSQL Config
      copy:
        dest: /opt/kafka_2.12-2.8.0/config/ksql-server.properties
        content: |
          bootstrap.servers=10.100.13.100:9092,10.100.13.102:9092,10.100.13.103:9092
          ksql.schema.registry.url=http://10.100.13.102:8081

- name: Setup Control Center
  hosts: control_center
  become: yes
  tasks:
    - name: Create Control Center Config
      copy:
        dest: /opt/kafka_2.12-2.8.0/config/control-center.properties
        content: |
          bootstrap.servers=10.100.13.100:9092,10.100.13.102:9092,10.100.13.103:9092
          confluent.controlcenter.connect.cluster=http://10.100.13.100:8083
          confluent.controlcenter.schema.registry.url=http://10.100.13.102:8081
```
