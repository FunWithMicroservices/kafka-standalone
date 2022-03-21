# Standalone Kafka Setup
This project includes a singlenode standalone Kafka Setup.

This Setup includes
- Zookeeper at Port 2181
- Kafka (Confluent Image) at Port 9092
- Schema Registry at Port 8081

**No Auth is included. All ports are open and any user and any**
**host can send messages to kafka**

## Requirements
- Docker and Docker-Compose - make sure to allow your docker setup
    at minimum 2GB RAM for this docker setup.

## Setup
```
docker-compose up -d
```

## Cheat Sheet

### Kafka
This commands are only executable correctly directly at the machine
where Kafka is running.

Consume Messages
```bash
docker-compose exec workshop-kafka-standalone-kafka-broker  \
  kafka-console-consumer \
  --bootstrap-server localhost:9092 \
  --topic {{ TOPIC_NAME }} \
  --from-beginning
```

Create Topic
```
docker-compose exec workshop-kafka-standalone-kafka-broker  \
    kafka-topics \
    --create \
    --topic {{ TOPIC_NAME }} \
    --partitions {{ PARTITION_COUNT }} \
    --replication-factor {{ REPLICATION_COUNT }} \
    --if-not-exists \
    --zookeeper zookeeper:2181
```

Describe Topic
```
docker-compose exec kafka  \
    kafka-topics \
    --describe \
    --topic {{ TOPIC_NAME }} \
    --zookeeper zookeeper:2181
```

### Schema Registry
Since Schema Registry is a rest api, you can execute the following
commands from everywhere where you have access to the host.

For further information and commands, take a look at the Official
Confluent API Doc:
https://docs.confluent.io/platform/current/schema-registry/develop/using.html

List all registered Schemas
```
curl -X GET http://{{HOST}}:8081/subjects
```

Register new schema
```
curl -X POST -H "Content-Type: application/vnd.schemaregistry.v1+json" \
--data '{"schema": "{\"type\":\"record\",\"name\":\"Location\",\"namespace\":\"iot\",\"fields\":[{\"name\":\"car_id\",\"type\":\"string\"},{\"name\":\"lat\",\"type\":\"float\"}, {\"name\":\"long\",\"type\":\"float\"},{\"name\":\"time\",\"type\":\"int\"}]}"}' \
http://{{HOST}}:8081/subjects/{{TOPIC_NAME}}-value/versions
```
