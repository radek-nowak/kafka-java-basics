## Getting started

```shell
docker-compose up -d
```

## Creating topics

```shell
 docker exec broker kafka-topics --bootstrap-server broker:9092 --create --topic demo
```

## Producer

> Producer is a Kafka client that publishes records to the Kafka cluster.

### Creating producers

There is a variety of ways to create a `Producer`. The basic way is to create a `Properties` object and set
the [producer properties](https://kafka.apache.org/documentation/#producerconfigs), minimally requiring:

- **bootstrap.servers**: list of host/port pairs to use for establishing the initial connection to the Kafka cluster
  This list should be in the form `host1:port1,host2:port2,...`. This list doesn’t need to include all brokers, since
  the producer will get more information after the initial connection. But it is rec‐
  ommended to include at least two, so in case one broker goes down, the producer
  will still be able to connect to the cluster.

- **key.serializer**: serializer class (actually the name of the class) for key that implements
  the `org.apache.kafka.common.serialization.Serializer`
  interface.
- **value.serializer**: serializer class that implements the `org.apache.kafka.common.serialization.Serializer`
  interface.

> Kafka brokers expect byte arrays as keys and values of messages. However any class that implements
> the `org.apache.kafka.common.serialization.Serializer` interface can be sent as key or value. But it means that the
> producer has to know how to convert those object to byte arrays.

### Sending messages

#### Prerequisites:

- [start dockers](#getting-started)
- [create kafka topic](#creating-topics)

Once properties are defined, we can pass properties as a parameter to the `KafkaProducer` constructor.
After that we can start creating `ProducerRecord`, which takes Kafka topic and message as parameters, and then send
a message.

```
ProducerRecord<String, String> record = new ProducerRecord<>("demo", "hello");

try {
    producer.send(record);
} catch (Exception e) {
    throw new RuntimeException(e);
}
```
## Full code
```java
package org.example;

import org.apache.kafka.clients.producer.KafkaProducer;
import org.apache.kafka.clients.producer.ProducerConfig;
import org.apache.kafka.clients.producer.ProducerRecord;
import org.apache.kafka.common.serialization.StringSerializer;

import java.util.Properties;

public class ProducerDemo {
    public static void main(String[] args) {

        // localhost
        String bootstrapServers = "127.0.0.1:9092";

        // create Producer properties
        Properties properties = new Properties();
        properties.setProperty(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, bootstrapServers);
        properties.setProperty(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());
        properties.setProperty(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());

        // create producer
        KafkaProducer<String, String> producer = new KafkaProducer<>(properties);

        // create record
        ProducerRecord<String, String> record = new ProducerRecord<>("demo", "hello");

        try {
            // send data - asynchronous
            producer.send(record);
        } catch (Exception e) {
            throw new RuntimeException(e);
        }
    }
}

```

To read the message from the topic execute the following command:

```shell
 docker exec -it broker kafka-console-consumer --bootstrap-server broker:9092 --topic demo --from-beginning
```
