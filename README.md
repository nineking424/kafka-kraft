# kafka-kraft

# Refference

- [https://medium.com/@fengliplatform/kafka-broker-setup-using-docker-image-33f7a8081a07](https://medium.com/@fengliplatform/kafka-broker-setup-using-docker-image-33f7a8081a07)
- [https://levelup.gitconnected.com/kraft-kafka-cluster-with-docker-e79a97d19f2c](https://levelup.gitconnected.com/kraft-kafka-cluster-with-docker-e79a97d19f2c)
- [https://kafka.apache.org/documentation/#quickstart](https://kafka.apache.org/documentation/#quickstart)

# Generate Cluster UUID(optional)

```bash
docker run --rm bitnami/kafka:latest /opt/bitnami/kafka/bin/kafka-storage.sh random-uuid
```

```bash
kafka 16:00:34.66
kafka 16:00:34.66 Welcome to the Bitnami kafka container
kafka 16:00:34.66 Subscribe to project updates by watching https://github.com/bitnami/containers
kafka 16:00:34.67 Submit issues and feature requests at https://github.com/bitnami/containers/issues
kafka 16:00:34.67

10HT3ErKTyKq9iXHk0EfBg
```

# single node

```bash
version: "3"
services:
  kafka01:
    image: 'bitnami/kafka:latest'
    volumes:
      - kafka01-data:/bitnami/kafka
    ports:
      - '9092'
      - '9093'
    environment:
      ALLOW_PLAINTEXT_LISTENER: yes
      KAFKA_ENABLE_KRAFT: yes
      KAFKA_KRAFT_CLUSTER_ID: 10HT3ErKTyKq9iXHk0EfBg
      KAFKA_CFG_PROCESS_ROLES: broker,controller
      KAFKA_CFG_CONTROLLER_LISTENER_NAMES: CONTROLLER
      KAFKA_CFG_LISTENERS: PLAINTEXT://:9092,CONTROLLER://:9093
      KAFKA_CFG_LISTENER_SECURITY_PROTOCOL_MAP: CONTROLLER:PLAINTEXT,PLAINTEXT:PLAINTEXT
      KAFKA_CFG_NODE_ID: 0
      KAFKA_CFG_ADVERTISED_LISTENERS: PLAINTEXT://kafka01:9092
      KAFKA_CFG_CONTROLLER_QUORUM_VOTERS: 0@kafka01:9093
volumes:
  kafka01-data:
    driver: local
```

# multi node

```bash
version: "3"
services:
  kafka01:
    image: 'bitnami/kafka:latest'
    volumes:
      - kafka01-data:/bitnami/kafka
    ports:
      - '9092'
      - '9093'
    environment:
      ALLOW_PLAINTEXT_LISTENER: yes
      KAFKA_ENABLE_KRAFT: yes
      KAFKA_KRAFT_CLUSTER_ID: 10HT3ErKTyKq9iXHk0EfBg
      KAFKA_CFG_PROCESS_ROLES: broker,controller
      KAFKA_CFG_CONTROLLER_LISTENER_NAMES: CONTROLLER
      KAFKA_CFG_LISTENERS: PLAINTEXT://:9092,CONTROLLER://:9093
      KAFKA_CFG_LISTENER_SECURITY_PROTOCOL_MAP: CONTROLLER:PLAINTEXT,PLAINTEXT:PLAINTEXT
      KAFKA_CFG_NODE_ID: 0
      KAFKA_CFG_ADVERTISED_LISTENERS: PLAINTEXT://kafka01:9092
      KAFKA_CFG_CONTROLLER_QUORUM_VOTERS: 0@kafka01:9093,1@kafka02:9093,2@kafka03:9093
  kafka02:
    image: 'bitnami/kafka:latest'
    volumes:
      - kafka02-data:/bitnami/kafka
    ports:
      - '9092'
      - '9093'
    environment:
      ALLOW_PLAINTEXT_LISTENER: yes
      KAFKA_ENABLE_KRAFT: yes
      KAFKA_KRAFT_CLUSTER_ID: 10HT3ErKTyKq9iXHk0EfBg
      KAFKA_CFG_PROCESS_ROLES: broker,controller
      KAFKA_CFG_CONTROLLER_LISTENER_NAMES: CONTROLLER
      KAFKA_CFG_LISTENERS: PLAINTEXT://:9092,CONTROLLER://:9093
      KAFKA_CFG_LISTENER_SECURITY_PROTOCOL_MAP: CONTROLLER:PLAINTEXT,PLAINTEXT:PLAINTEXT
      KAFKA_CFG_NODE_ID: 1
      KAFKA_CFG_ADVERTISED_LISTENERS: PLAINTEXT://kafka02:9092
      KAFKA_CFG_CONTROLLER_QUORUM_VOTERS: 0@kafka01:9093,1@kafka02:9093,2@kafka03:9093
  kafka03:
    image: 'bitnami/kafka:latest'
    volumes:
      - kafka03-data:/bitnami/kafka
    ports:
      - '9092'
      - '9093'
    environment:
      ALLOW_PLAINTEXT_LISTENER: yes
      KAFKA_ENABLE_KRAFT: yes
      KAFKA_KRAFT_CLUSTER_ID: 10HT3ErKTyKq9iXHk0EfBg
      KAFKA_CFG_PROCESS_ROLES: broker,controller
      KAFKA_CFG_CONTROLLER_LISTENER_NAMES: CONTROLLER
      KAFKA_CFG_LISTENERS: PLAINTEXT://:9092,CONTROLLER://:9093
      KAFKA_CFG_LISTENER_SECURITY_PROTOCOL_MAP: CONTROLLER:PLAINTEXT,PLAINTEXT:PLAINTEXT
      KAFKA_CFG_NODE_ID: 2
      KAFKA_CFG_ADVERTISED_LISTENERS: PLAINTEXT://kafka03:9092
      KAFKA_CFG_CONTROLLER_QUORUM_VOTERS: 0@kafka01:9093,1@kafka02:9093,2@kafka03:9093
volumes:
  kafka01-data:
    driver: local
  kafka02-data:
    driver: local
  kafka03-data:
    driver: local
```

# kafka-ui

```bash
version: "3"
services:
  kafka-ui:
    container_name: kafka-ui
    image: provectuslabs/kafka-ui:latest
    ports:
      - 8080:8080
    environment:
      KAFKA_CLUSTERS_0_NAME: multi-cluster
      KAFKA_CLUSTERS_0_BOOTSTRAPSERVERS: kafka01:9092,kafka02:9092,kafka03:9092
    networks:
      - kafka-multi_default
networks:
  kafka-multi_default:
    external: true
```