# Food ordering system overview #
Spring boot microservices with Clean & Hexagonal architectures, DDD, SAGA, Outbox, CQRS, Kafka.
During this project, I wrote some articles about architectures and technologies that I used in this project:
- Domain-Driven-Design(DDD) [Blog](https://itaha.hashnode.dev/microservices-domain-driven-design-ddd)
- Hexagonal architecture    [Blog](https://itaha.hashnode.dev/hexagonal-architecture)

### Microservices and Key Components:

1. **Order Service:**
- This microservice acts as the entry point for customer order requests.
- It receives requests via REST API and coordinates the order lifecycle.
- It uses hexagonal architecture and implements the SAGA pattern to ensure effective coordination of distributed transactions.
- The messaging component integrates with the Kafka cluster for asynchronous communication, enabling efficient application of the CQRS pattern.
2. **Payment Service:**
- Responsible for issuing and controlling order payments.
- Ensures the security and efficiency of financial transactions by integrating with reliable payment gateways.
- Its implementation adopts hexagonal architecture.
3. **Restaurant Service:**
- Manages order confirmation by restaurants and keeps customers informed about the preparation status.
- This microservice is essential for ensuring efficient communication between customers and partner establishments.
4. **Customer Service:**
- Service that provides services for consumer management.
5. **Kafka (Cluster):**
- The Kafka cluster is used for asynchronous communication between microservices using the CQRS (Command Query Responsibility Segregation) pattern, separating read and write operations to improve system performance and scalability.

Each component of the architecture is designed to play a specific role in the effectiveness of the Food Ordering System.
# Requirements
- [Java 17](https://www.oracle.com/technetwork/pt/java/javase/downloads/index.html)
- [Maven 3.9.x+](https://maven.apache.org/download.cgi)
- [PostgreSQL](https://www.postgresql.org/)
- [Apache Kafka](https://kafka.apache.org/)
- Docker
- Docker-Compose
- [Rancher Desktop](https://rancherdesktop.io/) or [Docker Desktop](https://www.docker.com/products/docker-desktop/) for Windows platform (optional)
- [Postman](https://www.postman.com/) (opcional)
- [GraphViz](https://graphviz.org/download/) (opcional)

### Dependencies
The project uses several essential libraries for its development and effective operation. The main libraries and dependencies include:
- **Spring Boot Starter Validation**: Essential for data validation, this Spring Boot library provides built-in support to ensure data integrity during CRUD operations.
- **Spring Boot Starter Web**: Used for web application development, this Spring Boot starter makes it easy to build RESTful APIs, contributing to the creation of the application's communication interface.
- **Spring Boot Starter Data JPA**: Facilitates integration with the PostgreSQL database, simplifying data persistence and retrieval operations.
- **Spring TX**: Spring package that provides support for transactions.
- **Spring Kafka**: Provides support for integration with Apache Kafka, enabling asynchronous communication between different components of the system.
- **Kafka AVRO Serializer**: Serializer plugin that helps in serializing/deserializing messages sent/received from Kafka using AVRO schemas.
- **Log4J**: Logging library.
- **Lombok**: A library that simplifies writing Java code, reducing the need for boilerplate code. Its use contributes to cleaner and more readable code.
- **Mockit**: Framework used in unit testing of applications.

# Customer Ordering Flow
![Food ordering system overview](.docs/food-ordering.png)
- **Step 1: Request**: The first step represents the customer's request submitted through a REST request which, upon arrival at the Order Service, is validated.
- **Step 2: Persistence**: If the request is compliant, the user and order data are registered in the database.
- **Step 3: Publishing a payment request**: After that, the Order Service publishes a payment request on the Kafka topic tp-payment-request.
- **Step 4: Consuming a payment request**: The payment request is consumed by the Payment Service.
- **Step 5: Persistence of payment data**: The Payment Service processes the request and registers the payment data in the database.
- **Step 6: Publishing the payment request response**: The Payment Service publishes the result of the payment step on the topic tp-payment-response.
- **Step 7: Processing the payment response**: The Order Service receives the event response from the payment step and performs the processing.
- **Step 8: Persistence**: The data from the payment step is persisted in the local database.
- **Step 9: Publishing restaurant confirmation**: Once the payment is made, the Order Service publishes an event on the topic tp-restaurant-approval-request asking the restaurant to confirm the order.
- **Step 10: Consuming the confirmation request**: The Restaurant Service receives the message and processes the request.
- **Step 11: Persistence of the confirmation request**: The confirmation data is persisted in the database.
- **Step 12: Publishing the confirmation response**: Once the restaurant has confirmed or rejected the order, the response is published on the topic tp-restaurant-approval-response.
- **Step 13: Consuming the confirmation response**: The Order Service retrieves the response to the restaurant's confirmation request from the topic and performs the processing.
- **Step 14: Persistence**: The restaurant confirmation data is persisted in the local database, ending the customer order flow.

### Order state transitions ###
![Order state transitions](.docs/order-state-transitions.png)

### SAGA and Outbox ###
The SAGA pattern is a design pattern used in distributed systems and microservices architecture to manage and coordinate complex, long-running transactions or business processes. It helps maintain data consistency across multiple services by breaking down a large transaction into smaller, discrete steps or actions.
<p>
The Outbox Pattern, also known as the Transactional Outbox Pattern, is a design pattern used in distributed systems to improve data consistency and communication between services. It addresses the challenges of maintaining data integrity and ensuring reliable communication between microservices in an asynchronous manner.

#### Successful flow
![Outbox successful flow](.docs/outbox-happy-flow.png)
#### Payment failure flow
![Outbox payment failure flow](.docs/outbox-payment-failure.png)
#### Restaurant approval failure flow
![Outbox restaurant approval failure flow](.docs/outbox-approval-failure.png)

