# Software Design Specification: Data Oracle Subsystem

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

1. User authentication and authorization: User access control is out of scope for this subsystem.

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

