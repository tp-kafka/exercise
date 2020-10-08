## Setup
Please use this devfile: https://raw.githubusercontent.com/tp-kafka/exercise/master/devfiles/ex01.devfile.yaml

Kafka Tools are located in */opt/bitnami/kafka/bin/*
For convenience, you can execute all commands and create the property file in that directory.

**Connection properties**
Create a file with properties required to connect to our Kafka cluster (replace placeholders for the username and password)

    echo "bootstrap.servers=pkc-4ygn6.europe-west3.gcp.confluent.cloud:9092\nssl.endpoint.identification.algorithm=https\nsecurity.protocol=SASL_SSL\nsasl.mechanism=PLAIN\nsasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username=\"<USERNAME>\" password=\"<PASSWORD>\";" > client.properties

Verifify if the file looks similar to this:

    bootstrap.servers=pkc-4ygn6.europe-west3.gcp.confluent.cloud:9092
    ssl.endpoint.identification.algorithm=https
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="KJHDGUMU8R3A" password="HJgJ8FREojHg/RRiK4SCfreHGuiiuhjhTU+gJHt6DR4xcV";

## Exercise
Using the commands provided below:
 1. Check which topics are available in the cluster 
 2. Create a new topic with 3 partitions and replication factor of 3
 3. Take turns to produce and consume messages from your topic; one person in the team produces, the other receives
 4. You can create and delete topics, but make sure that at the end your team has 1 available topic. You will need it in the following exercise

## Useful commands
**Listing topics**

    kafka-topics.sh \
    --list \
    --bootstrap-server pkc-4ygn6.europe-west3.gcp.confluent.cloud:9092 \
    --command-config client.properties

**Creating a new topic**

    kafka-topics.sh \
    --create \
    --replication-factor <no. replicas> \
    --partitions <no. partitions> \
    --topic <TOPIC_NAME> \
    --bootstrap-server pkc-4ygn6.europe-west3.gcp.confluent.cloud:9092 \
    --command-config client.properties

for example:

    kafka-topics.sh \
    --create \
    --replication-factor 3 \
    --partitions 3 \
    --topic dummy-topic \
    --bootstrap-server pkc-4ygn6.europe-west3.gcp.confluent.cloud:9092 \
    --command-config client.properties

**Topic properties**

    kafka-topics.sh \
    --describe \
    --topic <TOPIC_NAME> \
    --bootstrap-server pkc-4ygn6.europe-west3.gcp.confluent.cloud:9092 \
    --command-config client.properties

**Producing messages**

    kafka-console-producer.sh \
    --broker-list pkc-4ygn6.europe-west3.gcp.confluent.cloud:9092 \
    --producer.config client.properties \
    --topic <TOPIC_NAME>

**Consuming messages**

    kafka-console-consumer.sh \
    --bootstrap-server pkc-4ygn6.europe-west3.gcp.confluent.cloud:9092 \
    --consumer.config client.properties \
    --topic <TOPIC_NAME> \
    --from-beginning \
    --max-messages 100

Note the parameter *--from-beginning*. If you omit it, you will see only messages which are sent to the topic AFTER you startet the consumer

**Deleting a topic**

    kafka-topics.sh \
    --delete  \
    --topic <TOPIC_NAME> \
    --bootstrap-server pkc-4ygn6.europe-west3.gcp.confluent.cloud:9092 \
    --command-config client.properties

**Resetting stream applications**

    kafka-streams-application-reset.sh \
	    --application-id <APPLICATION_ID> \
	    --bootstrap-servers pkc-4ygn6.europe-west3.gcp.confluent.cloud:9092\
	    --input-topics <TOPIC_NAME>
