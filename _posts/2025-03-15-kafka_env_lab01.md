---
layout: post
title: Kafka python consumers
date: 15-03-2025
categories: [DevOps, Kafka]
tags: [kafka, docker, devops]
---


## Lab summary

In this lab, we're creating our new consumer and producres using Python supported library; this is to demonstrate my understanding in the enviornment.  

> #### Lab objective
> This is to demonstrate the understanding of the technology along with containerization.  

## Lab summary setup

![Home lab setup](https://gist.github.com/user-attachments/assets/bd1c7328-d291-47d9-b566-8ff29b22e22f)




### Here's the lab setup
![alt](https://i.imgur.com/GGVpDkZ.png)




# Kafka Lab details

**Title: Kafka Consumer - using python**


### 1. Step one: the docker-compose yaml

This is the docker-compose.yml file I use to create the envrionment, this will save me time in the interconnection between my dockers. 


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

This is a simple python consumer script

In order to run the script inside the docker you need: 

1- run ``` source /opt/venv/bin/activate ``` command in order to load the confluent_kafka in the virtual enviornment mode.  

2- ``` Python3 main.py ``` to run the consumer.  

```py
from confluent_kafka import Consumer, KafkaError


conf = {
    'bootstrap.servers': 'kafka:9092',  # Internal Docker network name
    'group.id': 'my_group',
    'auto.offset.reset': 'earliest'
}

consumer = Consumer(conf)
consumer.subscribe(['kafka.learning.tweets'])

while True:
    msg = consumer.poll(1.0)
    if msg is None:
        continue
    if msg.error():
        print(f"Consumer error: {msg.error()}")
        continue
    print(f"Received message: {msg.value().decode('utf-8')}")

consumer.close()
```

### 3. Create a topic for the producre 

This is done inside our kafka_broker cotainer that's created inside our docker-compose.yml file

```bash
 ./kafka-topics.sh \
> --bootstrap-server localhost:9092 \
> --create \
> --topic kafka.learning.tweets \
> --partitions 1 \
> --replication-factor 1


```


### 4. Produce a message via kafka producer from within the kafka broker

This is for testing prurposes, I've utilzied the kafka broker to start the producer in order to verify connectivity of my kafka docker consumer

```bash
./kafka-console-producer.sh \
            --bootstrap-server localhost:9092 \
            --topic kafka.learning.tweets


```


### 5. Test the consumer

Here's kafka consumer in action
![Kafka consumer in action](https://i.imgur.com/DSgMOF1.png)

## References
- [Noor Saif - Gist] (https://gist.github.com/noornj/a578401795059922f5eb9f0499e5b64d)

