<!-- KB PRE-RESEARCH -->

# 1. Table of Contents

- [1. Table of Contents](#1-table-of-contents)
- [2. Introduction](#2-introduction)
- [3. Kafka](#3-kafka)
  - [3.1. Overview](#31-overview)
  - [3.2. Advantages](#32-advantages)
  - [3.3. Disadvantages](#33-disadvantages)
  - [3.4. Demo](#34-demo)
    - [3.4.1. Prerequisites](#341-prerequisites)
    - [3.4.2. Image overview](#342-image-overview)
      - [3.4.2.1. zookeeper:latest](#3421-zookeeperlatest)
      - [3.4.2.2. bitnami/kafka:3.4](#3422-bitnamikafka34)
      - [3.4.2.3. obsidiandynamics/kafdrop](#3423-obsidiandynamicskafdrop)
    - [3.4.3. Get started with docker-compose](#343-get-started-with-docker-compose)
- [4. AWS MSK](#4-aws-msk)
  - [4.1. Overview](#41-overview)
  - [4.2. Advantages](#42-advantages)
  - [4.3. Disadvantages](#43-disadvantages)
- [5. Logstash](#5-logstash)
  - [5.1. Overview](#51-overview)
  - [5.2. Advantages](#52-advantages)
  - [5.3. Disadvantages](#53-disadvantages)
  - [5.4. Demo](#54-demo)
    - [5.4.1. Prerequisites](#541-prerequisites)
    - [5.4.2. Get started with docker-compose](#542-get-started-with-docker-compose)

# 2. Introduction

For KB project, we need to prepare the knowledge-base in tech-stacks.

The stack that I research:

- Kafka
- AWS MSK
- Logstash.

# 3. Kafka

## 3.1. Overview

Apache Kafka is an **open-source** distributed **event streaming platform** used by thousands of companies for high-performance data pipelines, streaming analytics, data integration, and mission-critical applications.

## 3.2. Advantages

- High Throughput
  - 2ms latencies
  - 2mil write/s
- Scalable
  - Scalable production cluster
- Permanent Storage
  - Distributed storage
  - Durable
  - Fault-tolerant
- High Availability
  - Stretch clusters over zones
  - Connect separate clusters

## 3.3. Disadvantages

- Operational Overhead
  - Complex to setup
- Storage Costs
  - Need large storage
- Latency
  - Not for **ultra-low** latency messaging
- Low request size
  - 1MB by default (max 2GB)

## 3.4. Demo

In the demo, I use:

- [Apache kafka](https://kafka.apache.org/).
- [obsidiandynamics/kafdrop](https://hub.docker.com/r/obsidiandynamics/kafdrop)
- [bitnami/zookeeper](https://hub.docker.com/r/bitnami/zookeeper)
- [bitnami/kafka](https://hub.docker.com/r/bitnami/kafka)

### 3.4.1. Prerequisites

- [Install Docker](https://docs.docker.com/get-docker/)
- [Install Docker Compose](https://docs.docker.com/compose/install/)

### 3.4.2. Image overview

#### 3.4.2.1. zookeeper:latest

Zookeeper provides an in-sync view of the Kafka cluster.

- Handles the leadership election of Kafka brokers.
- Track topics are created or deleted.

#### 3.4.2.2. bitnami/kafka:3.4

Kafka handle the actual connections from the clients.

#### 3.4.2.3. obsidiandynamics/kafdrop

Kafdrop is a web UI for viewing Kafka topics and browsing consumer groups.

### 3.4.3. Get started with docker-compose

Create a `docker-compose-kafka.yml` file with the following content:

```yml
version: "3.8"

volumes:
  zookeeper_data:
    driver: local
  kafka_data:
    driver: local

services:
  kafdrop:
    container_name: kafdrop-kb
    image: obsidiandynamics/kafdrop
    restart: "no"
    ports:
      - "9000:9000"
    environment:
      KAFKA_BROKERCONNECT: "kafka:29092"
      JVM_OPTS: "-Xms16M -Xmx48M -Xss180K -XX:-TieredCompilation -XX:+UseStringDeduplication -noverify"
    depends_on:
      - "kafka"

  zookeeper:
    container_name: zookeeper-kb
    image: docker.io/bitnami/zookeeper:3.8
    ports:
      - "2181:2181"
    volumes:
      - "zookeeper_data:/bitnami"
    environment:
      - ALLOW_ANONYMOUS_LOGIN=yes

  kafka:
    container_name: kafka-bitnami-kb
    image: docker.io/bitnami/kafka:3.4
    ports:
      - "29092:29092"
      - "9092:9092"
    volumes:
      - "kafka_data:/bitnami"
    environment:
      - ALLOW_PLAINTEXT_LISTENER=yes
      - KAFKA_ENABLE_KRAFT=no
      - KAFKA_CFG_ZOOKEEPER_CONNECT=zookeeper:2181
      - KAFKA_CFG_AUTO_CREATE_TOPICS_ENABLE=true
      - KAFKA_CFG_LISTENERS=PLAINTEXT://:29092,CONTROLLER://:9093,EXTERNAL://:9092
      - KAFKA_CFG_ADVERTISED_LISTENERS=PLAINTEXT://kafka:29092,EXTERNAL://localhost:9092
      - KAFKA_CFG_LISTENER_SECURITY_PROTOCOL_MAP=CONTROLLER:PLAINTEXT,EXTERNAL:PLAINTEXT,PLAINTEXT:PLAINTEXT
      - KAFKA_CFG_MESSAGE_MAX_BYTES=200000000
      - KAFKA_CFG_MAX_PARTITION_FETCH_BYTES=200000000
      - KAFKA_CFG_MAX_REQUEST_SIZE=200000000
    depends_on:
      - zookeeper
```

Run the following command to start the cluster:

```bash
docker-compose -f docker-compose-kafka.yml up -d
```

- Open `http://localhost:9000/` to see the Kafdrop dashboard.
- To connect with kafka cluster
  - External - from outside the docker network: `localhost:9092`
  - Internal - from inside the docker network: `kafka:29092`

# 4. AWS MSK

## 4.1. Overview

Securely stream data with a fully managed highly available Apache Kafka service.

## 4.2. Advantages

- Fully Managed:
  - Eliminate operational overhead
  - High availability
- Easy Set Up:
  - No code changes required
  - Scale cluster capacity automatically.
- Easy Implement:
  - Easily deploy secure, compliant
  - Production-ready applications using native AWS integrations.

## 4.3. Disadvantages

- Vendor Lock-in:
  - Hard to another platform
- Cost:
  - No free plan
  - Suitable for business purpose
- Limited Configuration:
  - Advanced configuration options and customization might not be available

# 5. Logstash

## 5.1. Overview

Logstash is a free and open server-side data processing pipeline that ingests data from a multitude of sources, transforms it, and then sends it to your favorite "stash."

## 5.2. Advantages

**Data Collection:** logs, metrics, and event streams. It supports a wide range of input plugins, including file-based inputs, network protocols like syslog and HTTP, message queues like Kafka and RabbitMQ.

**Data Transformation:** Logstash provides a variety of filter plugins that enable you to transform and enrich your data before it gets indexed. These filters allow you to parse, split, merge, rename, and perform other operations on your data. Additionally, Logstash supports powerful pattern matching using regular expressions, making it easy to extract meaningful information from unstructured data.

**Scalability and Resilience:** Elastic Logstash is designed to handle large-scale data processing. It supports parallel processing and can scale horizontally by distributing the workload across multiple Logstash instances. This scalability ensures that Logstash can handle high-volume data streams and provides the ability to process and transform data in real-time. Additionally, Logstash supports fault tolerance and provides mechanisms for data resiliency, such as durable queueing and persistent storage, to ensure data integrity.

**Integration with Elastic Stack:** Elastic Logstash seamlessly integrates with other components of the Elastic Stack, including Elasticsearch for data storage and search, and Kibana for data visualization and analysis. It provides a unified and comprehensive solution for collecting, analyzing, and visualizing data, enabling you to gain valuable insights from your data effectively.

## 5.3. Disadvantages

**Complexity and Learning Curve**: Elastic Logstash can be complex to set up and configure, especially for users who are new to the tool or have limited experience with data processing pipelines. The configuration language (DSL) used by Logstash, known as the Logstash Configuration Language (LSL), requires some learning and understanding to effectively define data inputs, filters, and outputs. Users may need to invest time and effort in learning Logstash's syntax and best practices.

**Resource Intensive**: Logstash's data processing capabilities come at a cost of system resources. Depending on the volume and complexity of the data being processed, Logstash can require significant CPU and memory resources. In high-traffic environments, scaling Logstash horizontally across multiple instances may be necessary to handle the workload efficiently. Allocating sufficient resources and monitoring performance becomes crucial to maintain smooth data processing.

**Latency**: not suitable for ultra-low latency

**Need other storage systems**: Elastic Logstash is primarily designed as a data processing tool, not a persistent data storage solution. While it can temporarily buffer and queue incoming data, it relies on external data stores, such as Elasticsearch, for long-term storage and retrieval.

## 5.4. Demo

Base on the [Getting started with the Elastic Stack and Docker-Compose - Official document](https://www.elastic.co/blog/getting-started-with-the-elastic-stack-and-docker-composes). I use relative volumes for `certs` to retrieve `ca.crt` for `elasticsearch` implementation.

### 5.4.1. Prerequisites

- [Install Docker](https://docs.docker.com/get-docker/)
- [Install Docker Compose](https://docs.docker.com/compose/install/)
- System requirements of docker VM:
  - 4GB of RAM
  - 2 CPUs

### 5.4.2. Get started with docker-compose

Create a `docker-compose-logstash.yml` file with the following content:

```yml
version: "3.8"

volumes:
  esdata01:
    driver: local
  kibanadata:
    driver: local
  metricbeatdata01:
    driver: local
  filebeatdata01:
    driver: local
  logstashdata01:
    driver: local
  zookeeper_data:
    driver: local
  kafka_data:
    driver: local
networks:
  default:
    name: elastic
    external: false

services:
  setup:
    image: docker.elastic.co/elasticsearch/elasticsearch:${STACK_VERSION}
    volumes:
      - ./certs:/usr/share/elasticsearch/config/certs
    user: "0"
    command: >
      bash -c '
        if [ x${ELASTIC_PASSWORD} == x ]; then
          echo "Set the ELASTIC_PASSWORD environment variable in the .env file";
          exit 1;
        elif [ x${KIBANA_PASSWORD} == x ]; then
          echo "Set the KIBANA_PASSWORD environment variable in the .env file";
          exit 1;
        fi;
        if [ ! -f config/certs/ca.zip ]; then
          echo "Creating CA";
          bin/elasticsearch-certutil ca --silent --pem -out config/certs/ca.zip;
          unzip config/certs/ca.zip -d config/certs;
        fi;
        if [ ! -f config/certs/certs.zip ]; then
          echo "Creating certs";
          echo -ne \
          "instances:\n"\
          "  - name: es01\n"\
          "    dns:\n"\
          "      - es01\n"\
          "      - localhost\n"\
          "    ip:\n"\
          "      - 127.0.0.1\n"\
          "  - name: kibana\n"\
          "    dns:\n"\
          "      - kibana\n"\
          "      - localhost\n"\
          "    ip:\n"\
          "      - 127.0.0.1\n"\
          > config/certs/instances.yml;
          bin/elasticsearch-certutil cert --silent --pem -out config/certs/certs.zip --in config/certs/instances.yml --ca-cert config/certs/ca/ca.crt --ca-key config/certs/ca/ca.key;
          unzip config/certs/certs.zip -d config/certs;
        fi;
        echo "Setting file permissions"
        chown -R root:root config/certs;
        find . -type d -exec chmod 750 \{\} \;;
        find . -type f -exec chmod 640 \{\} \;;
        echo "Waiting for Elasticsearch availability";
        until curl -s --cacert config/certs/ca/ca.crt https://es01:9200 | grep -q "missing authentication credentials"; do sleep 30; done;
        echo "Setting kibana_system password";
        until curl -s -X POST --cacert config/certs/ca/ca.crt -u "elastic:${ELASTIC_PASSWORD}" -H "Content-Type: application/json" https://es01:9200/_security/user/kibana_system/_password -d "{\"password\":\"${KIBANA_PASSWORD}\"}" | grep -q "^{}"; do sleep 10; done;
        echo "All done!";
      '
    healthcheck:
      test: ["CMD-SHELL", "[ -f config/certs/es01/es01.crt ]"]
      interval: 1s
      timeout: 5s
      retries: 120

  es01:
    depends_on:
      setup:
        condition: service_healthy
    image: docker.elastic.co/elasticsearch/elasticsearch:${STACK_VERSION}
    labels:
      co.elastic.logs/module: elasticsearch
    volumes:
      - ./certs:/usr/share/elasticsearch/config/certs
      - esdata01:/usr/share/elasticsearch/data
    ports:
      - ${ES_PORT}:9200
    environment:
      - node.name=es01
      - cluster.name=${CLUSTER_NAME}
      - discovery.type=single-node
      - ELASTIC_PASSWORD=${ELASTIC_PASSWORD}
      - bootstrap.memory_lock=true
      - xpack.security.enabled=true
      - xpack.security.http.ssl.enabled=true
      - xpack.security.http.ssl.key=certs/es01/es01.key
      - xpack.security.http.ssl.certificate=certs/es01/es01.crt
      - xpack.security.http.ssl.certificate_authorities=certs/ca/ca.crt
      - xpack.security.transport.ssl.enabled=true
      - xpack.security.transport.ssl.key=certs/es01/es01.key
      - xpack.security.transport.ssl.certificate=certs/es01/es01.crt
      - xpack.security.transport.ssl.certificate_authorities=certs/ca/ca.crt
      - xpack.security.transport.ssl.verification_mode=certificate
      - xpack.license.self_generated.type=${LICENSE}
    mem_limit: ${ES_MEM_LIMIT}
    ulimits:
      memlock:
        soft: -1
        hard: -1
    healthcheck:
      test:
        [
          "CMD-SHELL",
          "curl -s --cacert config/certs/ca/ca.crt https://localhost:9200 | grep -q 'missing authentication credentials'",
        ]
      interval: 10s
      timeout: 10s
      retries: 120

  kibana:
    depends_on:
      es01:
        condition: service_healthy
    image: docker.elastic.co/kibana/kibana:${STACK_VERSION}
    labels:
      co.elastic.logs/module: kibana
    volumes:
      - ./certs:/usr/share/kibana/config/certs
      - kibanadata:/usr/share/kibana/data
    ports:
      - ${KIBANA_PORT}:5601
    environment:
      - SERVERNAME=kibana
      - ELASTICSEARCH_HOSTS=https://es01:9200
      - ELASTICSEARCH_USERNAME=kibana_system
      - ELASTICSEARCH_PASSWORD=${KIBANA_PASSWORD}
      - ELASTICSEARCH_SSL_CERTIFICATEAUTHORITIES=config/certs/ca/ca.crt
      - XPACK_SECURITY_ENCRYPTIONKEY=${ENCRYPTION_KEY}
      - XPACK_ENCRYPTEDSAVEDOBJECTS_ENCRYPTIONKEY=${ENCRYPTION_KEY}
      - XPACK_REPORTING_ENCRYPTIONKEY=${ENCRYPTION_KEY}
      - CSP_STRICT=false

    mem_limit: ${KB_MEM_LIMIT}
    healthcheck:
      test:
        [
          "CMD-SHELL",
          "curl -s -I http://localhost:5601 | grep -q 'HTTP/1.1 302 Found'",
        ]
      interval: 10s
      timeout: 10s
      retries: 120

  metricbeat01:
    depends_on:
      es01:
        condition: service_healthy
      kibana:
        condition: service_healthy
    image: docker.elastic.co/beats/metricbeat:${STACK_VERSION}
    command: metricbeat -e -strict.perms=false
    user: root
    volumes:
      - ./certs:/usr/share/metricbeat/certs
      - metricbeatdata01:/usr/share/metricbeat/data
      - "./metricbeat.yml:/usr/share/metricbeat/metricbeat.yml:rw"
      - "/var/run/docker.sock:/var/run/docker.sock:rw"
      - "/sys/fs/cgroup:/hostfs/sys/fs/cgroup:rw"
      - "/proc:/hostfs/proc:rw"
      - "/:/hostfs:rw"
    environment:
      - ELASTIC_USER=elastic
      - ELASTIC_PASSWORD=${ELASTIC_PASSWORD}
      - ELASTIC_HOSTS=https://es01:9200
      - KIBANA_HOSTS=http://kibana:5601
      - LOGSTASH_HOSTS=http://logstash01:9600

  filebeat01:
    depends_on:
      es01:
        condition: service_healthy
    image: docker.elastic.co/beats/filebeat:${STACK_VERSION}
    command: filebeat -e -strict.perms=false
    user: root
    volumes:
      - ./certs:/usr/share/filebeat/certs
      - filebeatdata01:/usr/share/filebeat/data
      - "./filebeat_ingest_data/:/usr/share/filebeat/ingest_data/"
      - "./filebeat.yml:/usr/share/filebeat/filebeat.yml:rw"
      - "/var/lib/docker/containers:/var/lib/docker/containers:rw"
      - "/var/run/docker.sock:/var/run/docker.sock:rw"
    environment:
      - ELASTIC_USER=elastic
      - ELASTIC_PASSWORD=${ELASTIC_PASSWORD}
      - ELASTIC_HOSTS=https://es01:9200
      - KIBANA_HOSTS=http://kibana:5601
      - LOGSTASH_HOSTS=http://logstash01:9600

  logstash01:
    depends_on:
      es01:
        condition: service_healthy
      kibana:
        condition: service_healthy
    image: docker.elastic.co/logstash/logstash:${STACK_VERSION}
    labels:
      co.elastic.logs/module: logstash
    user: root
    volumes:
      - ./certs:/usr/share/logstash/certs
      - logstashdata01:/usr/share/logstash/data
      - "./logstash_ingest_data/:/usr/share/logstash/ingest_data/"
      - "./logstash.conf:/usr/share/logstash/pipeline/logstash.conf:rw"
    environment:
      - xpack.monitoring.enabled=false
      - ELASTIC_USER=elastic
      - ELASTIC_PASSWORD=${ELASTIC_PASSWORD}
      - ELASTIC_HOSTS=https://es01:9200

  kafdrop:
    container_name: kafdrop-kb
    image: obsidiandynamics/kafdrop
    restart: "no"
    ports:
      - "9000:9000"
    environment:
      KAFKA_BROKERCONNECT: "kafka:29092"
      JVM_OPTS: "-Xms16M -Xmx48M -Xss180K -XX:-TieredCompilation -XX:+UseStringDeduplication -noverify"
    depends_on:
      - "kafka"

  zookeeper:
    container_name: zookeeper-kb
    image: docker.io/bitnami/zookeeper:3.8
    ports:
      - "2181:2181"
    volumes:
      - "zookeeper_data:/bitnami"
    environment:
      - ALLOW_ANONYMOUS_LOGIN=yes

  kafka:
    container_name: kafka-bitnami-kb
    image: docker.io/bitnami/kafka:3.4
    ports:
      - "29092:29092"
      - "9092:9092"
    volumes:
      - "kafka_data:/bitnami"
    environment:
      - ALLOW_PLAINTEXT_LISTENER=yes
      - KAFKA_ENABLE_KRAFT=no
      - KAFKA_CFG_ZOOKEEPER_CONNECT=zookeeper:2181
      - KAFKA_CFG_AUTO_CREATE_TOPICS_ENABLE=true
      - KAFKA_CFG_LISTENERS=PLAINTEXT://:29092,CONTROLLER://:9093,EXTERNAL://:9092
      - KAFKA_CFG_ADVERTISED_LISTENERS=PLAINTEXT://kafka:29092,EXTERNAL://localhost:9092
      - KAFKA_CFG_LISTENER_SECURITY_PROTOCOL_MAP=CONTROLLER:PLAINTEXT,EXTERNAL:PLAINTEXT,PLAINTEXT:PLAINTEXT
      - KAFKA_CFG_MESSAGE_MAX_BYTES=200000000
      - KAFKA_CFG_MAX_PARTITION_FETCH_BYTES=200000000
      - KAFKA_CFG_MAX_REQUEST_SIZE=200000000
    depends_on:
      - zookeeper
```

Run the following command to start the stack:

```bash
docker-compose up -d
```

<!-- KB PRE-RESEARCH END -->
