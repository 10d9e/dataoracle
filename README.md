# Software Design Specification: Data Oracle Subsystem

## Overview
The Data Oracle service is designed to pull "StateMarketDeal" structures into a local database from a lotus node for third party application consumption. It is implemented in the Go programming language (Golang) and includes a cache layer for efficient querying of "StateMarketDeals." The system utilizes the GORM (Go Object Relational Mapping) library for working with a PostgreSQL database.

