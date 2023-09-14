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
