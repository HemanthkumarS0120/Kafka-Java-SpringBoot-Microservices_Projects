# Kafka Java Spring Boot Microservices Project


A Spring Boot application demonstrating Apache Kafka integration for both plain string messaging and JSON object messaging. Includes REST API endpoints to publish messages to Kafka topics and consumers that log received messages.


---


## Table of Contents


- [Tech Stack](#tech-stack)
- [Project Structure](#project-structure)
- [Architecture Overview](#architecture-overview)
- [API Endpoints](#api-endpoints)
- [Kafka Components](#kafka-components)
- [Configuration](#configuration)
- [Getting Started](#getting-started)
- [Running the Application](#running-the-application)


---


## Tech Stack


| Technology      | Version          |
|-----------------|------------------|
| Java            | 21               |
| Spring Boot     | 3.4.6            |
| Spring Web      | -                |
| Spring Kafka    | -                |
| Lombok          | -                |
| Maven           | Wrapper included |


---


## Project Structure


```
src/
└── main/
    └── java/
        └── com/springboot/kafka_project/
            ├── config/
            │   └── KafkaTopicConfig.java          # Kafka topic bean definitions
            ├── controller/
            │   ├── MessageController.java          # Publishes plain string messages
            │   └── JsonMessageController.java      # Publishes JSON (User object) messages
            ├── kafkaconsumer/
            │   ├── KafkaConsumer.java              # Consumes plain string messages
            │   └── JsonKafkaConsumer.java          # Consumes JSON messages
            ├── kafkaproducer/
            │   ├── KafkaProducer.java              # Produces plain string messages
            │   └── JsonKafkaProducer.java          # Produces JSON-serialized User objects
            ├── payload/
            │   └── User.java                       # JSON payload model
            └── KafkaProjectApplication.java        # Application entry point
```


---


## Architecture Overview


```
HTTP Client
    │
    ├── GET /api/v1/kafka/publish?message=hello
    │       ↓
    │   MessageController → KafkaProducer ──► Kafka Topic (string)
    │                                              ↓
    │                                       KafkaConsumer (logs message)
    │
    └── POST /api/v1/kafka/publish  {User JSON}
            ↓
        JsonMessageController → JsonKafkaProducer ──► Kafka Topic (JSON)
                                                           ↓
                                                    JsonKafkaConsumer (logs message)
```


---


## API Endpoints


Base path: `/api/v1/kafka`


### Publish a Plain String Message


```
GET /api/v1/kafka/publish?message={text}
```


| Parameter | Type     | Description              |
|-----------|----------|--------------------------|
| `message` | `String` | Text to publish to Kafka |


**Response — 200 OK**
```
Message sent to the topic
```


**Example:**
```bash
curl "http://localhost:8080/api/v1/kafka/publish?message=HelloKafka"
```


---


### Publish a JSON (User) Message


```
POST /api/v1/kafka/publish
Content-Type: application/json
```


**Request Body**
```json
{
  "id": 1,
  "firstName": "Hemanth",
  "lastName": "Kumar"
}
```


**Response — 200 OK**
```
Json message sent to kafka topic
```


**Example:**
```bash
curl -X POST http://localhost:8080/api/v1/kafka/publish \
  -H "Content-Type: application/json" \
  -d '{"id":1,"firstName":"Hemanth","lastName":"Kumar"}'
```


---


## Kafka Components


### Producers


| Class                | Topic           | Message Type  |
|----------------------|-----------------|---------------|
| `KafkaProducer`      | Configured via properties | `String` |
| `JsonKafkaProducer`  | Configured via properties | `User` (JSON) |


Both producers use `KafkaTemplate` and read the topic name from `application.properties` via `@Value("${spring.kafka.topic.name}")`.


### Consumers


| Class               | Listens On      | Action                        |
|---------------------|-----------------|-------------------------------|
| `KafkaConsumer`     | String topic    | Logs received message via SLF4J |
| `JsonKafkaConsumer` | JSON topic      | Logs received User object     |


Consumer group ID is read from `${spring.kafka.consumer.group-id}`.


### Topic Config


`KafkaTopicConfig` defines the Kafka `NewTopic` bean(s) so topics are created automatically on startup.


---


## Configuration


Add the following to `src/main/resources/application.properties`:


```properties
spring.kafka.bootstrap-servers=localhost:9092
spring.kafka.topic.name=your_topic_name
spring.kafka.consumer.group-id=your_consumer_group
spring.kafka.consumer.auto-offset-reset=earliest
```


---


## Getting Started


### Prerequisites


- Java 21+
- Apache Kafka running locally (default port `9092`)
- Maven (or use the included `mvnw` wrapper)


### Start Kafka (using Docker)


```bash
docker run -d --name kafka \
  -p 9092:9092 \
  apache/kafka:latest
```


### Clone the Repository


```bash
git clone https://github.com/HemanthkumarS0120/Kafka-Java-SpringBoot-Microservices_Projects.git
cd Kafka-Java-SpringBoot-Microservices_Projects
```


---


## Running the Application


**Linux/Mac:**
```bash
./mvnw spring-boot:run
```


**Windows:**
```bash
mvnw.cmd spring-boot:run
```


The application starts on **http://localhost:8080**.



