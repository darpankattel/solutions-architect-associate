# AWS Elastic Compute Cloud (EC2)
A virtual PC over the cloud, Infrastructure as a Service.

The EC2 User Data Script runs with the root user.

## Instance Types
### General Purpose
Balanced networking, compute and memory, for web servers, code repositories, etc.

Eg; t2, t3, t4, m5, ...

### Compute Optimized
For compute bound applications (like, batch processing workloads, media transcoding, high performance web servers, high performance computing (HPC), scientific modeling, distributed analytics, dedicated gaming servers, CPU-based machine learning inference, etc.), benefits from high performance processors.

Eg; C4, C5, ...


### Memory Optimized
For workloads that process large datasets in memory, like, open source databases, in-memory caches, and real time big data analytics.

Eg; R series, X, U, Z, ...

### Accelerated Computing

This kind of instances use extra hardware accelerators or co-processors, like, GPU, to perform functions, such as floating point calculations, graphics processing, or data pattern matching, more efficientlty than is possible in software running on CPUs.

Eg; P series, G series, T, I, D, F, V

G means with GPU, maybe.

For training and inference of frontier models, at the trillion-parameter scale, for agentic and generative ai applications, including question answering, code generation, video and image generation, speech recognition, etc.

### Storage Optimized
These instances are designed for workloads that require high sequential read and write access to very large data sets on local storage.They are optimized to deliver millions of low-latency, random I/O operations per second (IOPS) to applications. For, RDBMS, real-time databases, NoSQL databases, real time analytics, maybe using apache spark, etc..

Eg; i series, D some series


### HPC optimised
For applications such as large, complex simulations and deep learning workloads, that benefit from high performance processors.

Eg; Hpc series

## Elastic IPs

We can only have 5 elastic IPs per account, but we can write to AWS to increase the number of it.


## Placement Groups
We can have control over how the EC2 instances are placed over in a region. Types:

### Cluster
Multiple EC2 instances clustered in a single AZ, with low latency. Has higher network connection, and are closedly connected. Good for tasks like, scientific computing, big data jobs, where high network throughput is required.

A problem with this is that, if an AZ fails, then all the EC2 instances are not available.

### Spread
Multiple EC2 instances spread across AZs, with 7 instances per AZ per Placement Groups. Useful for distributed tasks, higher availability things, and critical applications. Lower risks of failure as the instances are seperated into different physical hardwares, across AZs.

### Partitions
Multiple partitions spread across AZs, with 7 partitions per AZ per Placement Group, with each partitions having EC2 instances. Can have 100 of instances in a partition group. The instances in different partitions donot share a common rack, hence, a partition failurte won't affect other partitions. Can be used with, distributed databases, HDFS, etc.