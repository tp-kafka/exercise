# Exercise 02
The goal of this excercise is to get familiar with the usage of Kafka from a Java Application. 

## JUnit
We will start by experimenting with JUnit Tests and Embedded Kafka. Please use the devfile URL https://raw.githubusercontent.com/tp-kafka/exercise/master/devfiles/ex02-junit.devfile.yaml or clone https://github.com/tp-kafka/chat/ and checkout branch ex02/junit.

### Producing Messages
You will find predefined methods within the chat-service project in class ProduceExcerciseTest marked with 'TODO'. Run these tests with "mvn test" in the maven container.

- [ ] Use the KafkaTemplate (kafka) to produce a string to a topic.
- [ ] Use the KafkaTemplate (kafka) to produce a string with a given key to a topic.
- [ ] Use the KafkaTemplate (kafka) to produce a string without key to a specified partition of a topic.

### Consuming Messages
You will find predefined methods within the chat-service project in class KafkaTestConsumer marked with 'TODO' and a corresponding JUnit test "ConsumeExcerciseTest".

- [ ] Consume a simple string payload from topic "consumeString" by using the KafkaListener annotation
- [ ] Consume topic and partition (on which the message was received) as well as the string payload from topic "consumeMetadata" by using the KafkaListener, Payload and Header annotation and the KafkaHeaders class

## Spring Boot Kafka Application
Now we will work on our Chat Application Example. Please use the devfile URL https://raw.githubusercontent.com/tp-kafka/exercise/master/devfiles/ex02-app.devfile.yaml or clone https://github.com/tp-kafka/chat/ and checkout branch ex02/app.


## Group Exercise
- [ ] All teams configure the same topic "input" on the central kafka broker.
- [ ] All teams configure their team-name as group-id.
- [ ] All teams start their application and use it to send messages.
- [ ] All teams configure "chat" as group-id.
- [ ] All teams start their application and use it to send messages.
- [ ] Did you receive all messages in both cases? Where is the difference? 






## Bonus A: Headers
- [ ] Send Messages with custom headers. Use the KafkaTemplate in combination with ProducerRecord.

## Bonus B: Serde
- [ ] Reconfigure your JUnit Test to use the central kafka broker.
- [ ] Produce some messages to a topic of your choice.
- [ ] Consume your messages through the cli tools. 
- [ ] Change the 'spring.kafka.producer.value-serializer' property and produce messages with another type.
- [ ] Consume your new messages through the cli tools. 

## Bonus C: SSE
- [ ] Instead of just logging received chat messages, push them via SSE to Web-Clients


