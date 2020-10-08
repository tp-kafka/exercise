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

Now we need to enrich the chat messages in the filtered topic by adding the user data and write the resulting stream to the chat topic.
- [ ]  create a stream from `conf.filteredTopic()`. Use the provided Serdes.
- [ ]  ensure that chat messages are processed on the processor insance which contains the user data matching the userId in the chat message.
- [ ]  join the chat message with the user data. Use the `enrich` method to create a `RichChatMessage`. Messages without available user information should be filtered out.
- [ ]  produce the resulting stream to `conf.outputTopic()`. Use the provided serdes.

## Group Exercise
We will run the whole application in a distributed manner. Every team will start one component.
- [ ]  divide the components beween teams
* chat application
* spam processor
* user application
* user processor (> 1x)
- [ ]  configure topics to: `input` -> `filtered` -> `chat`
- [ ]  create some users and check if the Table is distributed between instances
- [ ]  create some chat messages and check if they are filtered and enriched correctly

## Bonus A: Windowing
Extend the spam processor by adding a filter which will supress users which send too many messages in a short timeframe. Use windowing to count the messages of a user and emit a block event if the count superceeds a threshold. Use these events to filter messages within the existing pipeline. Use windowing again to unblock users after a configurable duration.

## Bonus B: Distributed Data
Extend the rest Endpoint of the user processor so that you can query which instance of the application holds a certain key. Use `streams.queryMetadataForKey` to query the metadata for a key.

## Bonus C: Distributed Data 2
Extend the endpoint from Bonus B so that the endpoint does not return the application instance. Instead query the data and return it if the data should be available locally. Otherwise send a redirect to the correct host - assuming the activeHost field in the metadata represents a querieable Host.
