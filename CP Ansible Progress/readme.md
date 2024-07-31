# Ansible Playbook Progress (Skip tag Health Check)

## Data VM

| IP | Alias |  Install |
| ------ | ---- | ---- |
|10.100.13.172 | node1.kafka <br> node1.zookeeper <br> node1.schema <br> node1.connect | kakfa ❓ <br> zookeeper ✔ <br> schema registry ✖ <br> kafka connect ✖|
|10.100.13.173 | node2.kafka <br> node2.zookeeper <br> node2.schema <br> node2.c3 | kakfa ❓ <br> zookeeper ✔ <br> kafka rest ✔ <br> control center ✔|
|10.100.13.174 | node3.kafka <br> node3.zookeeper <br> node3.schema | kakfa ❓ <br> zookeeper ✔ <br> ksql ❓ |


## Result

### Ansible Playbook Task ✔

![alt text](https://github.com/DitoIhkam/ADI/blob/main/CP%20Ansible%20Progress/img/1.%20Ansible%20success%20with%20hosts.png?raw=true)

### Node 1 (10.100.13.172)

KAFKA❓

![alt text](https://github.com/DitoIhkam/ADI/blob/main/CP%20Ansible%20Progress/img/Screenshot%202024-07-31%20141819.png?raw=true)

ZOOKEEPER ✔

![alt text](https://github.com/DitoIhkam/ADI/blob/main/CP%20Ansible%20Progress/img/2.%20Deploy%201%20Zookeeper.png?raw=true)

SCHEMA REGISTRY ✖

![alt text](https://github.com/DitoIhkam/ADI/blob/main/CP%20Ansible%20Progress/img/5.%20Deploy%201%20Schema%20Regist%20Fail.png?raw=true)

KAFKA CONNECT ✖
![alt text](https://github.com/DitoIhkam/ADI/blob/main/CP%20Ansible%20Progress/img/10.%20Deploy%201%20Kafka%20Connect%20Failed.png?raw=true)


### Node 2 (10.100.13.173)

KAFKA❓

![alt text](https://github.com/DitoIhkam/ADI/blob/main/CP%20Ansible%20Progress/img/Screenshot%202024-07-31%20141819.png?raw=true)

ZOOKEEPER ✔

![alt text](https://github.com/DitoIhkam/ADI/blob/main/CP%20Ansible%20Progress/img/3.%20Deploy%202%20Zookeeper.png?raw=true)

KAFKA REST ✔

![alt text](https://github.com/DitoIhkam/ADI/blob/main/CP%20Ansible%20Progress/img/6.%20Deploy%202%20kafka-rest%20Success.png?raw=true)

CONTROL CENTER ✔

![alt text](https://github.com/DitoIhkam/ADI/blob/main/CP%20Ansible%20Progress/img/11%20control%20center.png?raw=true)

### Node 3 (10.100.13.174)

KAFKA❓

![alt text](https://github.com/DitoIhkam/ADI/blob/main/CP%20Ansible%20Progress/img/Screenshot%202024-07-31%20141819.png?raw=true)

ZOOKEEPER ✔

![alt text](https://github.com/DitoIhkam/ADI/blob/main/CP%20Ansible%20Progress/img/4.%20Deploy%203%20Zookeeper.png?raw=true)

KSQL ❓

![alt text](https://github.com/DitoIhkam/ADI/blob/main/CP%20Ansible%20Progress/img/7.%20Deploy%203%20ksql%20not%20found.png?raw=true)

## Problem

### Fail
- Node 1
	- Kafka
	- Schema Reg
	- Kafka Connect
- Node 2
	- Kafka
- Node 3
	- Kafka
	- ksql (Unknown)
- All Node
	- Wrong command to check kafka?
- Go to C3 (node 2 + port), 10.100.13.173:9021
