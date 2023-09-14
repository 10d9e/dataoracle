# Software Design Specification: Data Oracle Subsystem

Version: _FIRST DRAFT_

## Overview
The Data Oracle service is designed to pull "StateMarketDeal" structures into a local database from a lotus node for third party application consumption. It is implemented in the Go programming language (Golang) and includes a cache layer for efficient querying of "StateMarketDeals." The system utilizes the GORM (Go Object Relational Mapping) library for working with a PostgreSQL database.

## Context
In a broader context, the Data Oracle subsystem is part of a larger system that collects and processes market data related to various states. It is responsible for retrieving, storing, and serving "StateMarketDeals" data efficiently. This data is used by other components of the system for analytics and reporting purposes.

## Goals
The primary goals of the Data Oracle subsystem are as follows:

1. **Data Retrieval**: Retrieve "StateMarketDeals" data from external sources and store it in a local PostgreSQL database.

2. **Efficient Querying**: Implement a cache layer to enable fast querying of "StateMarketDeals" data.

3. **Data Consistency**: Ensure data consistency between the cache and the PostgreSQL database.

4. **Scalability**: Design the subsystem to be scalable to handle increasing volumes of data.

5. **Reliability**: Ensure high availability and reliability of the subsystem.

## Non-goals
The following are non-goals for the Data Oracle subsystem:

1. ~~User authentication and authorization: User access control is out of scope for this subsystem.~~ (Tentative: add simple auth for metering and throttling as required)

2. Real-time data updates: The subsystem does not provide real-time data updates; it operates on a periodic batch basis.

## Proposed Solution
To achieve the goals outlined above, the proposed solution includes the following components and design considerations:

### Data Retrieval
1. **Data Sources**: Define and integrate external data sources that provide "StateMarketDeals" data. These sources may include APIs, file imports, or other data feeds.

2. **Data Transformation**: Implement data transformation logic if needed to convert external data into the appropriate format for storage.

3. **Database Storage**: Use PostgreSQL as the database backend to store "StateMarketDeals" data. Utilize GORM for data modeling and interactions with the database.

### Cache Layer
1. **Cache Implementation**: Implement a cache layer using a suitable caching mechanism (e.g., Redis or in-memory cache). Cache entries should be indexed by relevant query parameters for fast retrieval.

2. **Cache Invalidation**: Implement cache invalidation strategies to keep the cache synchronized with the database. When new data is added or existing data is updated, ensure that the corresponding cache entries are updated or invalidated.

### Data Synchronization
1. **Scheduled Jobs**: Implement scheduled jobs or batch processes to periodically fetch updated data from external sources and update the database. This ensures that the local database is up to date.

2. **Concurrency Control**: Use appropriate concurrency control mechanisms to prevent data inconsistencies during updates and cache synchronization.

### Scalability
1. **Horizontal Scaling**: Design the subsystem to be horizontally scalable by distributing data processing tasks across multiple instances if necessary. Implement load balancing for incoming requests.

2. **Database Sharding**: Consider database sharding techniques if the volume of data grows significantly to ensure efficient data storage and retrieval.

### Reliability
1. **Error Handling**: Implement robust error handling and logging to capture and report any issues during data retrieval, storage, or cache operations.

2. **Monitoring and Alerts**: Set up monitoring and alerting systems to detect and respond to system failures or performance bottlenecks promptly.

3. **Backup and Recovery**: Implement regular database backups and define a disaster recovery plan to ensure data integrity and availability.

4. **High Availability**: Deploy the subsystem in a high-availability configuration to minimize downtime.

The Data Oracle subsystem will be able to efficiently retrieve, store, and serve "StateMarketDeals" data while maintaining data consistency and scalability.

## REST API Specification

### Overview
The REST API for querying `StateMarketDeals` provides third-party applications with a standardized and secure interface to access data from the Data Oracle subsystem. This API allows clients to retrieve `StateMarketDeals` data from both the database and the cache efficiently.

### API Endpoints
The API will consist of the following endpoints:

1. **Retrieve `StateMarketDeals` by ID**
   - **Endpoint**: `GET /api/state-market-deals/{id}`
   - **Description**: Retrieve a specific `StateMarketDeals` entry by its unique ID.
   - **Parameters**:
     - `{id}`: The unique identifier of the `StateMarketDeals` entry, defined as integer-casted filecoin addresses (ie. `f0xyz` with the `f0` dropped) 
   - **Response**:
     - `200 OK` with the retrieved data in the response body.
     - `404 Not Found` if the specified ID does not exist.

2. **Search for `StateMarketDeals`**
   - **Endpoint**: `GET /api/state-market-deals`
   - **Description**: Retrieve a list of `StateMarketDeals` entries based on query parameters.
   - **Parameters**:
     - Query parameters can be used to filter, sort, and paginate results.
   - **Response**:
     - `200 OK` with the list of matching `StateMarketDeals` entries in the response body.

### Authentication and Authorization
- Authentication and authorization mechanisms should be implemented to ensure that only authorized third-party applications can access the API. This may involve the use of API keys, OAuth 2.0, or other authentication methods.

### Rate Limiting
- Implement rate limiting to prevent abuse of the API and ensure fair usage.

### Pagination
- For endpoints that return multiple results (e.g., search), implement pagination using query parameters (e.g., `page` and `limit`) to allow clients to retrieve large result sets efficiently.

### Cache Integration
- The API should take advantage of the cache layer to improve query performance. Consider implementing caching strategies like time-based expiration or cache invalidation based on data updates.

### Error Handling
- Implement proper error handling and provide meaningful error responses, including status codes and error messages, for various scenarios, such as invalid requests or server errors.

### Request and Response Formats
- Define the expected request and response formats, which should typically be in JSON format for ease of use and consistency.

### Security
- Ensure that the API follows security best practices, including input validation, data sanitization, and protection against common security threats like SQL injection and XSS attacks.

### Documentation
- Provide comprehensive API documentation, including endpoint descriptions, query parameters, request/response examples, and authentication instructions.

### Monitoring and Logging
- Set up monitoring and logging for the API to track usage, performance, and errors. Use AWS CloudWatch or similar services for this purpose.

### Versioning (Optional)
- Consider implementing API versioning to ensure backward compatibility as the API evolves.

### Testing
- Thoroughly test the API endpoints with sample requests and edge cases to ensure functionality and correctness.

### System Design
![image](https://github.com/jlogelin/dataoracle/assets/1556714/e24eb4c9-3b5c-40f7-81e6-e3d712321d72)

## Alternative Cloud-based Design

An alternative design for periodically querying an external API for data and storing it into a local PostgreSQL database using AWS services can leverage serverless and managed services to reduce infrastructure management overhead. Below is an outline of this alternative design:

### Architecture Overview:
The proposed architecture consists of the following AWS services:

1. **AWS Lambda**: To execute code that queries the external API and stores data in the PostgreSQL database.

2. **Amazon RDS (Relational Database Service)**: To host the PostgreSQL database.

3. **Amazon EventBridge (formerly CloudWatch Events)**: To schedule and trigger the Lambda function periodically.

4. **Amazon API Gateway (Optional)**: To expose an HTTP API for manual data retrieval or management.

### Design Steps:

1. **Create a PostgreSQL Database on RDS**:
   Set up an Amazon RDS instance running PostgreSQL as your database. Configure security groups and credentials appropriately.

2. **Define the Data Model**:
   Define the data model and schema for the data you'll be storing in the PostgreSQL database, similar to step 3 in the previous design.

3. **Develop the Lambda Function**:
   Create an AWS Lambda function in your preferred programming language (e.g., Node.js, Python, Go) to perform the following tasks:
   
   - Query the external API to retrieve data.
   - Transform and process the data as needed.
   - Store the processed data in the PostgreSQL database using libraries suitable for your chosen runtime (e.g., Node.js - pg-promise, Python - SQLAlchemy).

   Ensure the Lambda function has the necessary IAM roles and permissions to interact with both the external API and the RDS database.

4. **Schedule Lambda Execution**:
   Use Amazon EventBridge (formerly CloudWatch Events) to schedule the Lambda function to run at your desired intervals (e.g., daily, hourly). Create a rule that triggers the Lambda function at the specified schedule.

5. **Error Handling and Logging**:
   Implement proper error handling within the Lambda function to capture and log any issues. You can use AWS CloudWatch Logs for centralized logging.

6. **Testing and Monitoring**:
   Test your Lambda function to ensure it retrieves data correctly and stores it in the database. Set up CloudWatch Alarms and Metrics for monitoring Lambda performance and database health.

7. **Optional: Expose an API**:
   If you need manual data retrieval or management, consider creating an Amazon API Gateway with an associated Lambda function to expose an HTTP API endpoint for these operations.

8. **Deploy and Monitor**:
   Deploy the Lambda function and configure CloudWatch Events for scheduling. Monitor the system's performance, errors, and logs using AWS CloudWatch.

### Advantages of this Design:

- **Serverless**: No need to manage server instances. AWS Lambda automatically scales based on demand.

- **Managed Database**: Amazon RDS handles database maintenance, backups, and scaling.

- **Scalability**: Both Lambda and RDS can scale horizontally to handle increased workloads.

- **High Availability**: AWS services are designed for high availability and fault tolerance.

- **Security**: IAM roles and permissions can be tightly controlled for security.

### AWS RDS Pricing

Below is a pricing breakdown of running an [extra large postgresql instance on AWS](https://calculator.aws/#/addService/RDSPostgreSQL).

Monthly Cost for RDS Proxy (Monthly): 525.60 USD

Amazon RDS PostgreSQL instances cost (Monthly): 3,118.56 USD

Storage pricing (Monthly): 588.80 USD

Total: 4,232.96 USD

### References and Prior art

[fil naive marketwatch](https://github.com/ribasushi/fil-naive-marketwatch). [ribasushi](https://github.com/ribasushi). Retrieved on Sept 2nd.
