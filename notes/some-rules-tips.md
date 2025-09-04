# Things to be careful on
## **1. What These Phrases Actually Mean and What Services to Choose**

### **Minimizing Operational Overhead**

* **Meaning:** Reduce the burden of manual management, patching, scaling, monitoring, etc.
* **Choose:** **Managed services, serverless, automation**.
* **Examples:**

  * Use **RDS (managed DB)** instead of EC2 with self-managed database.
  * Use **AWS Lambda** instead of managing EC2 servers.
  * Use **AWS Elastic Beanstalk/Fargate** instead of EC2 for deployments.
* **Rule of Thumb:** Go "hands-off" whenever possible.

---

### **MOST High-Performing Solution**

* **Meaning:** Prioritize **performance (speed, low latency, high throughput)** over cost or simplicity.
* **Choose:** Services with **low latency, caching, high IOPS, optimized compute**.
* **Examples:**

  * Use **Amazon ElastiCache (Redis/Memcached)** for database query acceleration.
  * Use **Provisioned IOPS EBS** instead of General Purpose SSD when database I/O is heavy.
  * Use **Global Accelerator or CloudFront** for fastest global content delivery.
* **Rule of Thumb:** Performance = caching, provisioned resources, edge networks.

---

### **Cost-Efficient, Highly Available**

* **Meaning:** Balance **low cost with no downtime**.
* **Choose:** **S3 Standard-IA, Spot Instances, Auto Scaling, Multi-AZ**.
* **Examples:**

  * For web app: Use **Auto Scaling with On-Demand + Spot**.
  * For storage: **S3 Standard-IA** (less frequent but still fast access).
  * For DB: Use **RDS Multi-AZ (not Multi-Region unless disaster recovery is asked)**.
* **Rule of Thumb:** Do not over-provision, but never single point of failure.

---

### **Increase the Fault Tolerance**

* **Meaning:** System should remain functional even if components fail.
* **Choose:** **Multi-AZ, Multi-Region, Load Balancers, Queues**.
* **Examples:**

  * Use **ALB with multiple EC2 instances across AZs**.
  * Use **SQS to decouple services** so one failure doesn't cascade.
  * For critical apps: Use **Route 53 failover routing** with health checks.
* **Rule of Thumb:** Fault tolerance = redundancy + decoupling.

---

### **LEAST Operational Overhead**

* **Meaning:** *Even less hands-on work than "minimizing operational overhead"*. Go for fully managed, serverless, or AWS-native where AWS does nearly everything.
* **Choose:** **Lambda, DynamoDB, S3, API Gateway, CloudFront, Aurora Serverless**.
* **Examples:**

  * Instead of managing EC2 load balancer and servers → **API Gateway + Lambda**.
  * Instead of self-managed MySQL → **Aurora Serverless v2**.
* **Rule of Thumb:** Think "set it and forget it".

---

## **2. The (Select TWO) Pattern – How to Think**

This is where many candidates get confused because sometimes **two options work together (serially)**, sometimes **two are separate but both required**.

### How to decide?

* **If the question is asking for a *complete solution*, pick complementary answers.**
  Example: *"Make the app highly available and improve performance"* →

  * Option A: Add Auto Scaling across two AZs.
  * Option B: Add CloudFront for caching.
    **Both needed together.**

* **If the question is asking for *all that apply* to meet requirements, pick independent answers.**
  Example: *"What steps can reduce cost and improve fault tolerance?"* →

  * Option A: Use Spot Instances for worker nodes.
  * Option B: Enable Multi-AZ for database.
    **Both individually meet parts of the requirement.**

* **If one option alone already satisfies the full requirement, and the second is redundant, it’s likely a trap.**

---

## **3. Cheat Sheet – Quick Rules for AWS Exam Traps**

1. **Keywords Map to Well-Architected Pillars:**

   * Cost → Cost Optimization
   * High-performing → Performance Efficiency
   * Fault tolerance → Reliability
   * Operational overhead → Operational Excellence

2. **Minimize vs Least Operational Overhead:**

   * *Minimize*: Reduce work but some management is okay.
   * *Least*: Go fully serverless/managed.

3. **High Availability ≠ Fault Tolerance**

   * HA: Reduce downtime (Multi-AZ, health checks).
   * FT: Continue running even with failure (active-active, multi-region).

4. **Select TWO Strategy:**

   * Look for answers that either:

     * **Complement each other to form a solution**, or
     * **Independently meet different parts of the requirement**.

5. **When in doubt:**

   * **Prefer managed over self-managed**.
   * **Prefer multi-AZ over single-AZ**.
   * **Avoid over-engineering unless question says "MOST high-performing" or "maximum fault tolerance".**
