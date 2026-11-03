# Scalable E-Commerce Microservices Platform

A distributed e-commerce application built with **Java, Spring Boot, React, and Apache Kafka**. This project explores microservices architecture, asynchronous communication, service discovery, API routing, and independent data management to model the core workflow of an online shopping platform.

## Overview

I developed this project to explore how an e-commerce application can be designed as a collection of independent services rather than a single monolithic application.

The platform separates product management, order processing, inventory management, and payment simulation into individual microservices. Apache Kafka enables asynchronous communication between services, while Spring Cloud Eureka and an API Gateway support service discovery and centralized request routing.

### Key Components

* **Product Service:** Manages the product catalog.
* **Order Service:** Creates orders and tracks their status.
* **Inventory Service:** Manages inventory and stock reservations.
* **Payment Service:** Simulates payment processing.
* **API Gateway:** Provides a centralized entry point for API requests.
* **Service Registry:** Uses Eureka for service discovery.
* **Common Library:** Contains shared event contracts and reusable components.
* **React Frontend:** Provides the user interface.

## Architecture

The application follows a microservices architecture with event-driven communication.

```text
                      React Frontend
                            |
                            v
                       API Gateway
                            |
          +-----------------+-----------------+
          |                 |                 |
          v                 v                 v
    Product Service    Order Service    Inventory Service
                            |                 ^
                            |                 |
                            v                 |
                       Apache Kafka ----------+
                            |
                            v
                     Payment Service
                            |
                            v
                       Apache Kafka
                            |
                            v
                       Order Service

                Eureka Service Registry
                  Service Discovery
```

The diagram illustrates the main components and the intended event-driven workflow. Actual request routing and event topics depend on the application configuration.

## Order Processing Workflow

The application uses an asynchronous workflow to coordinate order processing.

```text
Order Created
     |
     v
   PENDING
     |
     v
OrderCreatedEvent
     |
     v
Inventory Reservation
     |
     v
InventoryReservedEvent
     |
     v
Payment Simulation
     |
     v
PaymentCompletedEvent
     |
     v
  COMPLETED
```

### How It Works

1. A customer submits an order through the application.
2. The Order Service saves the order with an initial `PENDING` status.
3. The Order Service publishes an `OrderCreatedEvent` to Kafka.
4. The Inventory Service consumes the event and attempts to reserve the required stock.
5. The Inventory Service publishes an `InventoryReservedEvent`.
6. The Payment Service processes the inventory event and simulates a payment.
7. The Payment Service publishes a `PaymentCompletedEvent`.
8. The Order Service consumes the payment event and updates the order status to `COMPLETED`.

This workflow demonstrates asynchronous communication between services without requiring every service to be called synchronously during order processing.

**Note:** The workflow above describes the intended successful order path. Production systems also need explicit handling for insufficient stock, payment failures, retries, duplicate events, and partially completed transactions.

## Technology Stack

| Category          | Technologies                                           |
| ----------------- | ------------------------------------------------------ |
| Backend           | Java 17, Spring Boot                                   |
| Service Discovery | Spring Cloud Netflix Eureka                            |
| API Routing       | Spring Cloud Gateway                                   |
| Event Streaming   | Apache Kafka                                           |
| Persistence       | Spring Data JPA                                        |
| Databases         | MySQL / PostgreSQL, depending on service configuration |
| Shared Components | Lombok, MapStruct                                      |
| Frontend          | React, Axios, React Router                             |
| Infrastructure    | Docker, Docker Compose                                 |
| API Testing       | Postman, cURL                                          |

## Project Structure

```text
Scalable-E-Commerce-Microservices-Platform/
|
├── service-registry/
├── api-gateway/
├── product-service/
├── order-service/
├── inventory-service/
├── payment-service/
├── common-lib/
├── frontend/
├── postman/
├── docs/
├── scripts/
├── docker-compose.yml
├── pom.xml
└── README.md
```

The backend services are organized into separate modules. Depending on the service, the source code may include packages for:

```text
controller/
service/
repository/
entity/
dto/
mapper/
event/
config/
exception/
```

This separation helps keep HTTP request handling, business logic, persistence, and event communication organized.

## Features

* **Modular backend:** Separates product, order, inventory, and payment responsibilities.
* **Service discovery:** Uses Eureka to support service registration and discovery.
* **Centralized API access:** Routes requests through an API Gateway.
* **Event-driven processing:** Uses Kafka for asynchronous communication.
* **Order lifecycle management:** Tracks order status during processing.
* **Inventory management:** Supports inventory operations and stock reservation workflows.
* **Payment simulation:** Demonstrates payment-related event processing without requiring a real payment provider.
* **Containerized infrastructure:** Uses Docker Compose to run supporting services.
* **Web interface:** Uses React for the frontend.

## Prerequisites

Install the following tools before running the application:

* Java Development Kit (JDK) 17 or the version required by the project configuration
* Apache Maven 3.8+
* Docker Desktop with Docker Compose
* Node.js and npm
* Git

Check your installations:

```bash
java -version
mvn -version
docker --version
docker compose version
node --version
npm --version
```

## Getting Started

### 1. Clone the Repository

```bash
git clone https://github.com/heshmasree2809/Scalable-E-Commerce-Microservices-Platform
cd Scalable-E-Commerce-Microservices-Platform
```


### 2. Start the Infrastructure

From the project root, run:

```bash
docker compose up -d
```

Check the running containers:

```bash
docker compose ps
```

The Docker Compose configuration is expected to start the required infrastructure, such as Kafka, Zookeeper, and the configured databases.

Typical ports used by this project are:

| Component         | Port |
| ----------------- | ---: |
| Eureka Dashboard  | 8761 |
| API Gateway       | 8080 |
| Product Service   | 8081 |
| Order Service     | 8082 |
| Inventory Service | 8083 |
| Payment Service   | 8084 |
| MySQL             | 3307 |
| Kafka             | 9092 |
| Zookeeper         | 2181 |

These are expected ports based on the project configuration and should be checked against `docker-compose.yml` and each service's configuration files.

### 3. Build the Backend

From the project root, run:

```bash
mvn clean install -DskipTests
```

This builds the Maven project and its modules without running the tests.

To execute the tests during the build, use:

```bash
mvn clean verify
```

### 4. Start the Microservices

Start the services in separate terminals. Run each command from the project root.

**Terminal 1 — Service Registry**

```bash
cd service-registry
mvn spring-boot:run
```

**Terminal 2 — API Gateway**

```bash
cd api-gateway
mvn spring-boot:run
```

**Terminal 3 — Product Service**

```bash
cd product-service
mvn spring-boot:run
```

**Terminal 4 — Inventory Service**

```bash
cd inventory-service
mvn spring-boot:run
```

**Terminal 5 — Order Service**

```bash
cd order-service
mvn spring-boot:run
```

**Terminal 6 — Payment Service**

```bash
cd payment-service
mvn spring-boot:run
```

Start the Service Registry first and allow it to initialize before starting the other services. Confirm that the working directory and service configuration are correct if any command fails.

### 5. Verify Service Registration

Open the Eureka dashboard:

```text
http://localhost:8761
```

Check whether the configured services appear in the registered instances list.

The expected services are:

* API-GATEWAY
* PRODUCT-SERVICE
* ORDER-SERVICE
* INVENTORY-SERVICE
* PAYMENT-SERVICE

The displayed names depend on each service's `spring.application.name` configuration.

### 6. Start the Frontend

Open another terminal:

```bash
cd frontend
npm install
npm start
```

If the frontend is configured to use port 3000, open:

```text
http://localhost:3000
```

The frontend's startup command may differ if the project uses Vite or another build tool. Check `frontend/package.json` for the available scripts.

## API Testing

The following examples illustrate how to test the main API workflows through the API Gateway.

**Base URL:**

```text
http://localhost:8080
```

The examples assume that the gateway exposes the listed routes and that the request fields match the application's DTOs.

### 1. Create a Product

```bash
curl -X POST "http://localhost:8080/api/products" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "MacBook Pro",
    "description": "Laptop for development and productivity",
    "price": 2499.99,
    "category": "Electronics",
    "stock": 25
  }'
```

### 2. Retrieve All Products

```bash
curl "http://localhost:8080/api/products"
```

### 3. Retrieve a Product by ID

```bash
curl "http://localhost:8080/api/products/1"
```

Replace `1` with an existing product ID.

### 4. Create Inventory

```bash
curl -X POST "http://localhost:8080/api/inventory?productId=1&quantity=100"
```

This example assumes that the inventory endpoint accepts `productId` and `quantity` as query parameters.

### 5. Retrieve Inventory for a Product

```bash
curl "http://localhost:8080/api/inventory/product/1"
```

### 6. Create an Order

```bash
curl -X POST "http://localhost:8080/api/orders" \
  -H "Content-Type: application/json" \
  -d '{
    "userId": 1,
    "orderItems": [
      {
        "productId": 1,
        "quantity": 2,
        "price": 2499.99
      }
    ]
  }'
```

If the request is accepted, the API should return the created order and its initial status, assuming the endpoint follows the workflow described above.

### 7. Check Order Status

```bash
curl "http://localhost:8080/api/orders/1"
```

Replace `1` with the ID returned when creating the order.

If all required services process the events successfully, the order should eventually reach `COMPLETED`. The actual processing time depends on the running services and Kafka configuration.

### 8. Retrieve Payment Details

```bash
curl "http://localhost:8080/api/payments/order/1"
```

### 9. Retrieve Orders for a User

```bash
curl "http://localhost:8080/api/orders/user/1"
```

These endpoint examples should be verified against the controllers and gateway route configuration before being treated as a complete API reference.

## Observing Kafka Events

Kafka makes it possible for services to exchange events without depending on direct synchronous calls for every processing step.

If your Docker Compose configuration uses a Kafka container named `ecommerce-kafka`, you can inspect the order events with:

```bash
docker exec -it ecommerce-kafka kafka-console-consumer \
  --bootstrap-server localhost:9092 \
  --topic order-created-topic \
  --from-beginning
```

To inspect other events, substitute the topic name:

```text
inventory-reserved-topic
payment-completed-topic
```

The container name and topic names must match the actual configuration. If the command fails, check `docker compose ps` and the Kafka topic configuration.

### End-to-End Workflow

To test the complete successful order flow:

1. Create a product.
2. Initialize inventory for the product.
3. Create an order using the product ID.
4. Check the initial order status.
5. Observe the service logs and Kafka events.
6. Retrieve the order again to check its updated status.
7. Verify the inventory and payment records.

Use the IDs returned by the API instead of assuming that newly created records always receive ID `1`.

## Database Design

The application follows a database-per-service approach, where each microservice owns its persistence responsibilities.

The main data entities described by this project include:

### Product

Typical fields:

* `id`
* `name`
* `description`
* `price`
* `category`
* `stock`

### Order

Typical fields:

* `id`
* `userId`
* `totalAmount`
* `status`
* `createdAt`

### Order Item

Typical fields:

* `id`
* `orderId`
* `productId`
* `quantity`
* `price`

The actual schema, field types, constraints, and relationships are defined by the application's entity classes and database configuration.

## API Endpoint Reference

The following table summarizes the intended endpoints. Confirm the actual routes and HTTP methods in the service controllers.

| Service   | Method | Endpoint                                       | Purpose                               |
| --------- | ------ | ---------------------------------------------- | ------------------------------------- |
| Product   | POST   | `/api/products`                                | Create a product                      |
| Product   | GET    | `/api/products`                                | Retrieve products                     |
| Product   | GET    | `/api/products/{id}`                           | Retrieve a product                    |
| Product   | PUT    | `/api/products/{id}`                           | Update a product                      |
| Product   | DELETE | `/api/products/{id}`                           | Delete a product                      |
| Inventory | POST   | `/api/inventory?productId={id}&quantity={qty}` | Create inventory                      |
| Inventory | GET    | `/api/inventory`                               | Retrieve inventory records            |
| Inventory | GET    | `/api/inventory/{id}`                          | Retrieve an inventory record          |
| Inventory | GET    | `/api/inventory/product/{productId}`           | Retrieve inventory for a product      |
| Order     | POST   | `/api/orders`                                  | Create an order                       |
| Order     | GET    | `/api/orders`                                  | Retrieve orders                       |
| Order     | GET    | `/api/orders/{id}`                             | Retrieve an order                     |
| Order     | GET    | `/api/orders/user/{userId}`                    | Retrieve a user's orders              |
| Payment   | GET    | `/api/payments`                                | Retrieve payments                     |
| Payment   | GET    | `/api/payments/{id}`                           | Retrieve a payment                    |
| Payment   | GET    | `/api/payments/order/{orderId}`                | Retrieve payment details for an order |

## Testing Approach

The project can be validated at several levels:

* **Unit tests:** Verify individual service and business logic.
* **Integration tests:** Validate interactions between application components and persistence.
* **API tests:** Check HTTP methods, request payloads, responses, and error handling.
* **Event-flow tests:** Verify Kafka message production and consumption.
* **End-to-end tests:** Validate the order workflow from product selection to payment simulation.

Run the tests that are actually implemented in the repository. The presence of a testing strategy in this README does not imply that every test category is already covered.

## Troubleshooting

### Port Already in Use

On Windows, identify the process using a port:

```cmd
netstat -ano | findstr :8080
```

The final column contains the process ID (PID). To stop that process, run the following command after verifying that it is safe to terminate:

```cmd
taskkill /PID <PID> /F
```

Replace `<PID>` with the actual process ID. Alternatively, configure the application to use a different available port.

### Services Not Registering with Eureka

* Confirm that the Service Registry starts successfully.
* Check each service's Eureka client configuration.
* Verify that the service names and registry URL are correct.
* Review application logs for connection or configuration errors.

### Kafka Connection Errors

Check the containers:

```bash
docker compose ps
```

View the logs:

```bash
docker compose logs kafka
```

The service name `kafka` must match the corresponding service in `docker-compose.yml`.

### Database Connection Problems

* Confirm that the database container is running.
* Check the configured database name, port, username, and password.
* Verify that the database has initialized successfully.
* Review the relevant service's application configuration and logs.

Avoid committing real credentials to GitHub. Use environment variables or an untracked local environment file for secrets, and provide a sanitized example configuration when needed.

### Order Stuck at `PENDING`

* Confirm that the Inventory and Payment Services are running.
* Check whether the expected Kafka topics exist.
* Inspect consumer logs for deserialization or processing errors.
* Verify that the events are published and consumed successfully.
* Check whether an exception prevents the Order Service from updating the status.

If your Kafka container is named `ecommerce-kafka`, list the topics with:

```bash
docker exec -it ecommerce-kafka kafka-topics \
  --list \
  --bootstrap-server localhost:9092
```

## Stopping the Application

Stop each Spring Boot service with `Ctrl+C` in its terminal.

Stop the Docker infrastructure:

```bash
docker compose down
```

To remove the Docker Compose volumes and their persisted data:

```bash
docker compose down -v
```

**Warning:** The `-v` option can permanently delete the database and other data stored in the associated Docker volumes.

## Future Enhancements

Potential improvements include:

* Implementing a Saga pattern for coordinating distributed transactions.
* Adding retry policies and dead-letter topics for failed event processing.
* Introducing circuit breakers with Resilience4j.
* Adding distributed tracing with Zipkin or OpenTelemetry.
* Centralizing application logs and monitoring.
* Automating builds and tests through a CI/CD pipeline.
* Container orchestration with Kubernetes.
* Integrating a real payment provider.
* Developing product recommendations and an administrative analytics dashboard.
* Improving resilience, security, and performance under load.

These are possible extensions rather than claims about features already implemented.

## What I Learned

Through this project, I explored:

* Structuring a backend application using microservices.
* Designing asynchronous workflows with Kafka.
* Separating service responsibilities and persistence.
* Configuring service discovery and API routing.
* Running application infrastructure with Docker Compose.
* Testing REST APIs and investigating distributed application failures.
* Understanding the challenges of coordinating multiple services in an order-processing workflow.

## License

This project is intended for educational and demonstration purposes. Add a specific open-source license if you decide to distribute the code under one.
