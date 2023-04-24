## Producer

> Producer is a Kafka client that publishes records to the Kafka cluster.

### Creating producers

There is a variety of ways to create a `Producer`. The basic way is to create a `Properties` object and set
the [producer properties](https://kafka.apache.org/documentation/#producerconfigs), minimally requiring:

- **bootstrap.servers**: list of host/port pairs to use for establishing the initial connection to the Kafka cluster
  This list should be in the form `host1:port1,host2:port2,...`.
- **key.serializer**: serializer class for key that implements the `org.apache.kafka.common.serialization.Serializer`
  interface.
- **value.serializer**: serializer class that implements the `org.apache.kafka.common.serialization.Serializer`
  interface.

Once properties are defined, we can pass then as a parameter to the `KafkaProducer` constructor.
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
    ProducerRecord<String, String> record = new ProducerRecord<>("demo", "hello2");

    // send data - asynchronous
    producer.send(record);

    // flush data - synchronous
    producer.flush();

    // flush and close producer
    producer.close();
  }
}

```