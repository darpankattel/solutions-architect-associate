# Databases in AWS

## How to choose the right database?

## Amazon RDS
Managed SQL database, uses PostgreSQL/MySQL/Oracle/SQL Server/DB2/MariaDB/Custom databases.

RDS Custom for access to customize the underlying instance (oracle and sql server)

### Usecases
To store relational datasets (OLTP), perform SQL queries, transactions, etc..

## Amazon Aurora
Cloud-native, fully managed database; storage and compute seperated, otherwise same as RDS.

Stored data is replicated in 6 replicas, across 3 AZs, highly available, self-healing, auto-scaling.

It is fully compatible with MySQL and PostgreSQL. It has 5x the throughput of MySQL and 3x of PostgreSQL.

<Talk about the backup and restore options for Aurora>

### Aurora Serverless
No capacity planning, so for unpredictable / intermittent workloads

### Aurora Global
Upto 16 DB read instances in each region, < 1 second storage replication

### Aurora Machine Learning
Enables a native SQL user to integrate Machine Learning-based predictions to an application without knowing or understanding any machine learning algorithms. It is natively integrated with two machine learning services; Amazon Sagemaker and Amazon Comprehend.

### Aurora Database Cloning


### Usecases
Same as RDS, but with less maintenance/more flexibility/more performance/ more features

## Amazon ElastiCache
For caching, use redis or memecached. Very low latency.

### Usecases
Frequent reads, less writes, or caching results from db queries.
In Key/Value stores.
For storing session data for websites.

## Amazon DynamoDB
This is a serverless, no-sql database, with milisecond latency. Highly available, multi AX by default. Can replace ElastiCache as a Key/Value store, using TTL feature.

We can use DAX for read cache, with microsecond latency.

It also has features for event processing, using DynamoDB streams, to integrate with Lambda or kinesis data streams.

Has global table feature. DynamoDB is great for evolving schemas.

### Usecases
Serverless applications development

## Amazon S3
This is a object storage service, serverless, scales infinitely, with max object size 5TG.

## Document DB
Document DB is AWS implementation of MongoDB, so NoSQL database.
It is used to store, query and index json data.
DocumentDB storage automatically grows in increments of 10GB


## Amazon Neptune
This is a fully managed graph database, quite like Neo4j. For highly connected datasets, optimized for such complex and hard queries. Great for knowledge graphs, fraud detection, recommendation engines, social networking, etc..

## Amazon Keyspaces (for Apache Cassandra)

## Amazon QLDB

## Amazon Timestream