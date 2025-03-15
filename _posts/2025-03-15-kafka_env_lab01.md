---
layout: post
title: Kafka python consumers
date: 15-03-2025
categories: [DevOps, Kafka]
tags: [kafka, docker, devops]
---


## Lab summary

In this lab, we're creating our new consumer and producres using Python supported library; this is to demonstrate our understanding in the enviornment.   

## Lab summary setup

![Home lab setup](https://gist.github.com/user-attachments/assets/bd1c7328-d291-47d9-b566-8ff29b22e22f)









## Kafka Lab source code
**Kafka Consumer - using python**


### 1. Step one: the docker-compose yaml
```yaml
version: '3'

services:
kafka:
image: bitnami/kafka:latest
container_name: kafka_broker
environment:
- KAFKA_ENABLE_KRAFT=yes
- KAFKA_CFG_NODE_ID=1
- KAFKA_CFG_PROCESS_ROLES=controller,broker
- KAFKA_CFG_CONTROLLER_QUORUM_VOTERS=1@kafka:9093
- KAFKA_CFG_LISTENERS=PLAINTEXT://:9092,CONTROLLER://:9093
- KAFKA_CFG_ADVERTISED_LISTENERS=PLAINTEXT://kafka:9092
- KAFKA_CFG_LISTENER_SECURITY_PROTOCOL_MAP=CONTROLLER:PLAINTEXT,PLAINTEXT:PLAINTEXT
- KAFKA_CFG_CONTROLLER_LISTENER_NAMES=CONTROLLER
- ALLOW_PLAINTEXT_LISTENER=yes
ports:
- "9092:9092"

kafka_consumer:
image: ubuntu:latest
container_name: kafka_consumer
depends_on:
- kafka
command: >
/bin/bash -c "apt update && apt install -y python3 python3-pip python3-venv &&
python3 -m venv /opt/venv &&
source /opt/venv/bin/activate &&
pip install confluent-kafka &&
tail -f /dev/null"

kafka_producer:
image: ubuntu:latest
container_name: kafka_producer
depends_on:
- kafka
command: >
/bin/bash -c "apt update && apt install -y python3 python3-pip python3-venv &&
python3 -m venv /opt/venv &&
source /opt/venv/bin/activate &&
pip install confluent-kafka &&
tail -f /dev/null"
```

### 2. Step two: Python consumer code 
```Python

```


## References
- [Noor Saif - Gist] (https://gist.github.com/noornj/a578401795059922f5eb9f0499e5b64d)

