# Confluent-parallel-consumer-application

## Prerequisites

- Install Docker Desktop (version 4.0.0 or later) or Docker Engine (version 19.03.0 or later) if you don’t already have it
- Install the Docker Compose plugin if you don’t already have it. This isn’t necessary if you have Docker Desktop since it includes Docker Compose.
- Start Docker if it’s not already running, either by starting Docker Desktop or, if you manage Docker Engine with systemd, via systemctl
- Verify that Docker is set up properly by ensuring no errors are output when you run docker info and docker compose version on the command line

## To start it locally

### Setup environment

Launch it by running:

```bash
docker compose up -d
```

### Create a topic

```bash
docker exec -it broker bash
```

Now use the following command to create the topic:

```bash
kafka-topics --create --topic parallel-consumer-input-topic --bootstrap-server broker:9092 --replication-factor 1 --partitions 1
```

## Performance Test

Open a new terminal and window then run this command:

```bash
docker exec -it broker bash
``` 
Now use the following command to create the topic:
    
```bash
kafka-topics --create --topic perftest-parallel-consumer-input-topic --bootstrap-server broker:9092 --replication-factor 1 --partitions 6
``` 

Keep this terminal window open as you’ll need to run a console-producer in a few steps.

### Produce 10,000 records to the topic

run the following command to write 10,000 small dummy records to the input topic

```bash
seq 1 10000 | kafka-console-producer --topic perftest-parallel-consumer-input-topic --bootstrap-server broker:9092
```

### Build the project

Wrap the project
```bash
gradle wrapper
```

Compile it
```bash 
gradle shadowJar
```

To run the **MultithreadedKafkaConsumer** approach
```bash
java -cp build/libs/confluent-parallel-consumer-application-standalone-0.0.1-all.jar io.confluent.developer.MultithreadedKafkaConsumerPerfTest configuration/perftest-kafka-consumer.properties
```

To run the **ParallelConsumer** approach
```bash
java -cp build/libs/confluent-parallel-consumer-application-standalone-0.0.1-all.jar io.confluent.developer.ParallelConsumerPerfTest configuration/perftest-parallel-consumer.properties
```

### Results

#### MultithreadedKafkaConsumer
[main] INFO io.confluent.developer.MultithreadedKafkaConsumer - Total time to consume **10000** records: **107.66 seconds**


#### ParallelConsumer
[main] INFO io.confluent.developer.ParallelConsumerPerfTest - Time to consume **10000** records: **1.35 seconds**

Have fun with it!

### See more details

[How to use the Confluent Parallel Consumer](https://developer.confluent.io/tutorials/confluent-parallel-consumer/kafka.html#create-the-confluent-parallel-consumer-performance-test)