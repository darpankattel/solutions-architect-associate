# Health Checks - Route53
## **1. Types of Route 53 Health Checks (L6)**

There are **three types of health checks (L6)** you can configure in Route 53:

1. **Endpoint Health Check**

   * Monitors the health of a resource directly (e.g., EC2, ALB, ELB, S3 bucket website, on-prem server with public IP).
   * Uses HTTP, HTTPS, or TCP requests to determine health.
   * Can pass/fail based on:

     * **HTTP status codes** (like 2xx, 3xx are healthy, 4xx/5xx unhealthy)
     * **First 5120 bytes (5 KB)** of the response body (for string matching—e.g., check if "Success" appears in the response).

   **Example:**

   * You set a health check for your app hosted in `us-east-1`.
   * It makes an HTTP GET to `https://example.com/health`.
   * If it gets 200 OK and the body contains `"OK"`, it is considered healthy.

---

2. **Calculated Health Check**

   * Does **not monitor an endpoint directly**.
   * Combines results from **other health checks (up to 256 child health checks)** using **Boolean logic (AND, OR, NOT)**.
   * Useful when you want to create complex rules.

   **Example:**

   * You have three endpoints: `us-east-1`, `us-west-1`, and `eu-west-1`.
   * You create a calculated health check:

     * Mark as **healthy if at least two of the three are healthy (OR logic)**.
   * If only one endpoint is healthy, traffic might still be redirected based on your routing policy.

---

3. **CloudWatch Alarm Health Check (Private Health Check)**

   * Used for **private resources in a private VPC** (Route 53 cannot access them directly).
   * Instead of probing an endpoint, it uses **CloudWatch alarms** to determine health.
   * Good for internal applications (databases, private APIs).

   **Example:**

   * You have an internal database with a CPU alarm in CloudWatch.
   * If CPU > 80% for 5 minutes, the CloudWatch alarm goes into "ALARM" state → Route 53 health check marks it **unhealthy**.

---

## **2. Health Check Configuration Concepts**

* **Health Checkers (15 Global L6s)**

  * Route 53 health checks are performed from **15+ AWS global locations** (L6s = Level 6, meaning health checker nodes).
  * Each L6 periodically checks your endpoint (every 30s or 10s if fast check enabled).

* **Threshold (default 3)**

  * Means: For an endpoint to be marked healthy/unhealthy, it must fail or pass **3 consecutive checks**.
  * This avoids flapping (rapid switching due to temporary network hiccups).

* **80% Rule**

  * If **≥80% of the health checkers** say it’s healthy → Route 53 marks it healthy.
  * If <80% say healthy → Route 53 marks it unhealthy.

**Example:**

* 15 health checkers.
* 12 report healthy (12/15 = 80%) → healthy.
* If only 11 report healthy (11/15 = 73%) → unhealthy.

---

## **3. First 5120 Bytes (5 KB) Rule**

* Route 53 only reads **first 5 KB of the HTTP(S) response body** when you enable string matching.
* You define a keyword or string to search (e.g., `"Service OK"`).
* If the keyword is found in the first 5 KB → health = healthy; else unhealthy.

**Example:**

* Your endpoint returns a JSON:

  ```json
  { "status": "Service OK", "uptime": 12345 }
  ```
* If `"Service OK"` is within first 5 KB → healthy.

---

## **4. Calculated Health Check in Detail**

* Can combine **up to 256 child health checks**.
* Can apply **AND/OR/NOT** logic.

**Example:**

* You have three data centers: A, B, C.
* You want your application to be considered healthy only if:

  * A **AND** B are healthy **OR** C is healthy.

This can be expressed in a calculated health check.

---

## **5. Private Health Checks (CloudWatch Alarms)**

* Used when endpoints are **not publicly accessible**.
* Route 53 cannot send health checkers into your VPC, so instead it relies on **CloudWatch metrics**.

**Example Usage:**

* You have an internal payroll system in a private VPC.
* You set a CloudWatch alarm: *“Database connections > 500”* → unhealthy.
* Route 53 health check is linked to this alarm → failover traffic to another region.

---

## **6. Use Cases**

* **Failover routing:** Primary server health check fails → traffic moves to DR (disaster recovery) site.
* **Multi-region high availability:** Use latency-based or weighted with health checks.
* **Hybrid cloud setups:** Monitor on-prem server and AWS resources together.
* **Private workloads:** Health check via CloudWatch metrics.

---

## **7. Example Exam-style Keywords & Questions**

* *“What is the default health check interval in Route 53?”* → 30s (10s optional).
* *“How does Route 53 determine endpoint health when 80% rule is applied?”* → ≥80% of health checkers report healthy.
* *“Can Route 53 health check monitor a private VPC endpoint?”* → Yes, via CloudWatch alarm.
* *“How many child health checks can a calculated health check include?”* → 256.
* *“What is the purpose of the 5120 bytes limit?”* → To minimize data transfer for string matching.

---

## **8. Disaster Recovery Example**

* **Active-Passive (Failover):**

  * Primary: `us-east-1` EC2 with a health check.
  * Secondary: S3 static website in `us-west-2`.
  * If primary health check fails (e.g., 3 consecutive failures from ≥80% L6s), Route 53 fails over to S3.

* **Active-Active:**

  * Use latency or weighted with health checks to distribute traffic and failover automatically.
