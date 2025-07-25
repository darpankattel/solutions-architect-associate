# AWS Elastic Compute Cloud (EC2)


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