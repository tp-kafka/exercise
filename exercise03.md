# Exercise 03
The goal of this excercise is to get familiar Kafka Streams. 

## Spam Processor
Now we will work on the spam-processor. Please use the devfile URL https://raw.githubusercontent.com/tp-kafka/exercise/master/devfiles/ex03-spam-processor.devfile.yaml or clone https://github.com/tp-kafka/spam-processor/ and checkout branch ex03. Note: find relevant lines by searching "TODO".

Get the processor to read ChatMessages from the input topic, filter out all messages containing illegal phrases and write the filtered stream to filtered. Please use the central kafka broker with topics prefixed with yout team name.

- [ ] configure team specific topic in `application.properties`
- [ ] create stream from the input topic configured in `conf`. Use the provided serdes for deserialization.
- [ ] write the resulting stream to the output topic configured in conf `conf`. Use the provided serdes for deserialization. You should now have copied the input stream to the output stream
- [ ] filter out illegal messages by invoking `containsBadWords` before writing to the output

## User Processor
Now we will work on the user-processor. Please use the devfile URL https://raw.githubusercontent.com/tp-kafka/exercise/master/devfiles/ex03-user-processor.devfile.yaml or clone https://github.com/tp-kafka/user-processor/ and checkout branch ex03. Note: find relevant lines by searching "TODO".

The user processor should enhance the chat messages, which contain only the user ids with the data captured by the user application. The user application is producing events to kafka when the REST interface is called. Program the user processor to aggregate those events into a KTable and check the functionality by using the rest interface of the user application to produce events and checking the root endpoint of the user processor, wich will return the local data of the KTable.

- [ ] create a stream from `conf.userTopic()`. Use the provided Serdes.
- [ ] create a materialized KTable from that stream by using `the description provided in `materializesUserTable()`
- [ ] check your table by opening the endpoint of the app
- [ ] produce some events by using the user application and check your table again


## Bonus A: Windowing

## Bonus B: Distributed Data
