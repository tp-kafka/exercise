Please use this devfile: https://raw.githubusercontent.com/tp-kafka/exercise/master/devfiles/ex04-schema-registry.devfile.yaml


1. Create a new maven project
2. Create an avro schema, and store it under *src/main/avro/user.avsc*

*user.avsc*

    {
      "namespace": "example.avro",
      "type": "record",
      "name": "User",
      "fields": [
        {
          "name": "name",
          "type": "string"
        },
        {
          "name": "favorite_number",
          "type": "int",
          "default": 7
        },
        {
          "name": "favorite_color",
          "type": [
            "string",
            "null"
          ]
        }
      ]
    }

3. in pom.xml add the following:



    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.avro</groupId>
                <artifactId>avro-maven-plugin</artifactId>
                <version>1.9.0</version>
                <executions>
                    <execution>
                        <phase>generate-sources</phase>
                        <goals>
                            <goal>schema</goal>
                        </goals>
                        <configuration>
                            <sourceDirectory>${project.basedir}/src/main/avro/</sourceDirectory>
                            <outputDirectory>${project.basedir}/src/main/java/</outputDirectory>
                        </configuration>
                    </execution>
                </executions>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>
        </plugins>
    </build>
    ...
    <dependency>
        <groupId>org.apache.kafka</groupId>
        <artifactId>kafka-clients</artifactId>
        <version>2.1.0</version>
    </dependency>
    <dependency>
        <groupId>org.apache.avro</groupId>
        <artifactId>avro</artifactId>
        <version>1.9.0</version>
    </dependency>
    <dependency>
        <groupId>io.confluent</groupId>
        <artifactId>kafka-avro-serializer</artifactId>
        <version>5.2.1</version>
    </dependency>
    ...
    <repositories>
        <repository>
            <id>confluent</id>
            <url>http://packages.confluent.io/maven/</url>
        </repository>
    </repositories>

4. Build the project. This should generate a new class, *example.avro.User*, and store it under *src/main/java*
5. Create a producer


    import example.avro.User;
    import io.confluent.kafka.serializers.AbstractKafkaAvroSerDeConfig;
    import io.confluent.kafka.serializers.KafkaAvroSerializer;
    import io.confluent.kafka.serializers.subject.TopicRecordNameStrategy;
    import org.apache.kafka.clients.producer.KafkaProducer;
    import org.apache.kafka.clients.producer.ProducerConfig;
    import org.apache.kafka.clients.producer.ProducerRecord;
    import org.apache.kafka.common.serialization.StringSerializer;
     
    import java.io.IOException;
    import java.util.Properties;
     
    public class Producer {
     
        public static void main(String[] args) throws IOException {
            Properties props = new Properties();
            props.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, "<KAFKA_URL>");
     
            props.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());
            props.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, KafkaAvroSerializer.class.getName());
     
            props.put(AbstractKafkaAvroSerDeConfig.VALUE_SUBJECT_NAME_STRATEGY, TopicRecordNameStrategy.class.getName());
            props.put(AbstractKafkaAvroSerDeConfig.KEY_SUBJECT_NAME_STRATEGY, TopicRecordNameStrategy.class.getName());
     
            props.put("schema.registry.url", "<SCHEMA_REGISTRY_URL>");
     
            KafkaProducer<String, User> producer = new KafkaProducer<>(props);
            User user = User.newBuilder().setName("Jimbo").setFavoriteNumber(7).setFavoriteColor("green").build();
     
            ProducerRecord<String, User> record = new ProducerRecord<>("avro-test", "1", user);
     
            try {
                producer.send(record).get();
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    }

6. Create a consumer


    import example.avro.User;
    import io.confluent.kafka.serializers.KafkaAvroDeserializer;
    import io.confluent.kafka.serializers.KafkaAvroDeserializerConfig;
    import org.apache.kafka.clients.consumer.ConsumerConfig;
    import org.apache.kafka.clients.consumer.ConsumerRecord;
    import org.apache.kafka.clients.consumer.ConsumerRecords;
    import org.apache.kafka.clients.consumer.KafkaConsumer;
    import org.apache.kafka.common.TopicPartition;
    import org.apache.kafka.common.errors.SerializationException;
    import org.apache.kafka.common.serialization.StringDeserializer;
     
    import java.time.Duration;
    import java.util.Collections;
    import java.util.Properties;
     
    public class Consumer {
     
        public static void main(String[] args) {
            Properties props = new Properties();
            props.put(ConsumerConfig.GROUP_ID_CONFIG, "dummy");
            props.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, "<KAFKA_URL>");
     
            props.put(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class.getName());
            props.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, KafkaAvroDeserializer.class.getName());
            props.put(KafkaAvroDeserializerConfig.SPECIFIC_AVRO_READER_CONFIG, "true");  // without this property messages would be deserialized to GenericRecord, not user-defined class
            props.put(ConsumerConfig.AUTO_OFFSET_RESET_CONFIG, "earliest");
     
            props.put("schema.registry.url", "<SCHEMA_REGISTRY_URL>");
     
            KafkaConsumer<String, User> consumer = new KafkaConsumer(props);
            consumer.subscribe(Collections.singletonList("avro-test"));
     
            try {
                while (true) {
                        ConsumerRecords<String, User> records = consumer.poll(Duration.ofSeconds(2));
     
                        for (ConsumerRecord<String, User> record : records) {
                            System.out.println("key: " + record.key() + ", " + record.value());
                        }
                }
            } finally {
                consumer.close();
            }
        }
     
    }
