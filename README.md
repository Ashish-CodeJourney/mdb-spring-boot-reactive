# Reactive Java Spring Boot with Spring Data MongoDB 

## Overview
This project demonstrates a simplified cash balance application using reactive Java Spring Boot and Spring Data MongoDB. It includes:
- CRUD operations with `ReactiveMongoRepository`
- CRUD operations with `ReactiveMongoTemplate`
- Multi-document transaction handling

## Supported Versions
- Java 17
- Spring Boot Starter Webflux 3.2.3
- Spring Boot Starter Reactive Data Mongodb 3.2.3
- Gradle 7.5.1

## Application Workflow
1. Creation of a bank account with a unique `accountNum`, initializing with a $0 balance.
2. Storage of accounts and their balances in the "accounts" collection.
3. Debit operations to increase the account balance.
4. Credit operations to decrease the account balance.
5. Transfer operations to move funds from one account to another.
6. Transaction lifecycle:
   - Operations are initially recorded in the "transactions" collection with a "PENDING" status.
   - Account balances are updated.
   - Transaction status is updated to "SUCCESS".
7. Rollback transactions and mark as "FAILED" if:
   - Insufficient balance for debit operations.
   - Account number is invalid.

## Setup Instructions
1. Ensure a MongoDB cluster is available.
2. Execute schema validation to enforce a non-negative balance:
```shell
mongosh "<MongoDB connection string>" --file setup.js
```
3. Create an `application.properties` file in the `resources` directory:
```properties
spring.data.mongodb.uri=<MongoDB connection string>
spring.data.mongodb.database=txn-demo
```
4. Compile the application:
```shell
./gradlew clean build
```
5. Run the application:
```shell
./gradlew bootRun
```

## API Endpoints

### Create Account
**POST** `/account` \
**Request Body**:
```json
{
  "accountNum": "<String>",
  "balance": "<Number>"
}
```
**Example**:
```shell
curl --location 'localhost:8080/account' \
--header 'Content-Type: application/json' \
--data '{
    "accountNum": "111111"
}'
```

### Retrieve Account
**GET** `/account/{accountNum}` \
**Example**:
```shell
curl --location 'localhost:8080/account/111111'
```

### Debit Account
**POST** `/account/{accountNum}/debit` \
**Request Body**:
```json
{
  "amount": "<Number>"
}
```
**Example**:
```shell
curl --location 'localhost:8080/account/111111/debit' \
--header 'Content-Type: application/json' \
--data '{
    "amount": 1000
}'
```

### Credit Account
**POST** `/account/{accountNum}/credit` \
**Request Body**:
```json
{
  "amount": "<Number>"
}
```
**Example**:
```shell
curl --location 'localhost:8080/account/111111/credit' \
--header 'Content-Type: application/json' \
--data '{
    "amount": 10000
}'
```

### Transfer Funds
**POST** `/account/{accountNum}/transfer` \
**Request Body**:
```json
{
  "to": "<String>",
  "amount": "<Number>"
}
```
**Example**:
```shell
curl --location 'localhost:8080/account/123456/transfer' \
--header 'Content-Type: application/json' \
--data '{
    "to": "1111111",
    "amount": 500
}'
```

## Testing with Postman
For functional testing, use the provided Postman collection to ensure API functionality:
1. Import the collection into Postman.
2. Set environment variables "host" and "port" in Postman.
3. Reset the database:
```shell
mongosh "<MongoDB connection string>" --file setup.js
```
4. Use the "Run collection" feature in Postman to execute all API calls sequentially and validate test results.

## Testing with Rapid API Client
For local testing, the Rapid API Client can be utilized. Make sure the application is running locally and configure the client accordingly.
