---
title: Setup Kafka for Local Development 
categories: Java Concurrency
tags: java
description: Set up a Kafka container for local development with TLS and SASL authentication enabled.

last_modified_at: 2025-03-08 05:35:00 -0530
---

### Prerequisites
- Docker


### Docker Compose 

```yaml
networks:
  local-dev-net:
    driver: bridge
volumes:
  kafka_data:
services:
  kafka:
    image: docker.io/bitnami/kafka:3.9
    ports:
      - "9094:9094"
    volumes:
      - "kafka_data:/bitnami"
    environment:
      # KRaft settings
      - KAFKA_CFG_NODE_ID=0
      - KAFKA_CFG_PROCESS_ROLES=controller,broker
      - KAFKA_CFG_CONTROLLER_QUORUM_VOTERS=0@kafka:9093
      # Listeners
      - KAFKA_CFG_LISTENERS=PLAINTEXT://:9092,CONTROLLER://:9093,EXTERNAL://:9094
      - KAFKA_CFG_ADVERTISED_LISTENERS=PLAINTEXT://:9092,EXTERNAL://localhost:9094
      - KAFKA_CFG_LISTENER_SECURITY_PROTOCOL_MAP=CONTROLLER:PLAINTEXT,PLAINTEXT:PLAINTEXT,EXTERNAL:PLAINTEXT
      - KAFKA_CFG_CONTROLLER_LISTENER_NAMES=CONTROLLER
      - KAFKA_CFG_INTER_BROKER_LISTENER_NAME=PLAINTEXT
```
