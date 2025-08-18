# 🚀 Amazon EC2 – Purchasing Options

EC2 provides flexible ways to pay for compute, depending on **workload patterns, cost optimization, and business needs**. Let’s break them down.

---

## 🔹 1. **On-Demand Instances**

💡 **"Pay as you go."**

* **Billing**:

  * Linux/Windows: billed **per second** after first minute.
  * Other OS (RHEL, SUSE, etc.): billed **per hour**.
* **No upfront payment**, **highest cost per unit**.
* **No commitment** → spin up, use, and shut down whenever you want.

✅ **Use Cases**

* Short-term, unpredictable workloads.
* Testing, prototyping, dev environments.
* Apps where you cannot predict usage.

🔖 **Example**:
Running a new startup’s web app — usage is unpredictable, so On-Demand is safer than committing upfront.

---

## 🔹 2. **Reserved Instances (RI)**

💡 **"Pay less if you commit to long-term."**

* Reservation period: **1 year** or **3 years**.
* **Up to 72% cheaper** than On-Demand.
* Commitment is tied to:

  * **Instance type** (e.g., `m5.large`)
  * **Region or AZ**
  * **Tenancy** (Shared/Dedicated)
  * **OS**
* Payment options:

  * **No Upfront**,
  * **Partial Upfront**,
  * **All Upfront** (biggest discount).
* **Scope**:

  * **Regional** → discount applies to any instance in that region.
  * **Zonal** → reserve specific capacity in one AZ.

✅ **Use Cases**

* Predictable, steady workloads like databases or ERP apps.
* Long-running servers.

🔖 **Example**:
Running an **RDS database** for 3 years with predictable load → reserve instances to save money.

---

### 🔸 **Convertible Reserved Instances**

💡 **"Flexibility with lower discount."**

* Up to **66% discount** (vs. 72%).
* Can **change instance family, type, OS, tenancy, or scope**.
* Still long-term commitment.

✅ **Use Cases**

* When you know workloads will be long-term but not sure what instance type you’ll need in the future.

---

## 🔹 3. **Savings Plans**

💡 **"Discounts like RI, but more flexible."**

* Commitment to **spend (\$/hr)** for 1 or 3 years.
* Apply across **different instance families, regions, sizes, OS, tenancy**.
* Two types:

  * **Compute Savings Plans** → most flexible (applies across EC2, Fargate, Lambda).
  * **EC2 Instance Savings Plans** → narrower (specific instance family, but flexible across sizes/regions).

✅ **Use Cases**

* Long-term, consistent workloads where you want flexibility to change instance families/regions.

🔖 **Example**:
Commit to spending **\$50/hour on EC2** for 3 years → AWS automatically applies discounts whether you run `m5.large` in us-east-1 or `c6g.xlarge` in eu-west-1.

---

## 🔹 4. **Spot Instances**

💡 **"Unused capacity at up to 90% discount, but not reliable."**

* Prices fluctuate based on capacity & demand.
* You define a **max price** you’re willing to pay.
* If market price exceeds your max → **instance is terminated/stopped with 2 minutes notice**.
* Can use **Spot Block** → reserve for 1–6 hours without interruptions.

✅ **Use Cases**

* Batch processing, big data, rendering, distributed workloads.
* Jobs that are fault-tolerant and flexible in start/end time.
  ❌ Not suitable for **critical apps** (e.g., production DBs).

🔖 **Example**:
Video rendering for a film studio → run hundreds of Spot Instances at 90% discount, but okay if some are reclaimed.

---

### 🔸 **Spot Fleets**

* A **set of Spot Instances (with optional On-Demand)** to meet target capacity.
* Can choose multiple pools (instance types + AZs).
* Allocation strategies:

  * **lowestPrice** → cheapest pool.
  * **diversified** → spread across pools for reliability.
  * **capacityOptimized** → pick pool with best capacity.
  * **priceCapacityOptimized (recommended)** → best balance.

✅ **Use Case**
Running large workloads like web crawling or ML training across hundreds of instances at lowest cost.

---

## 🔹 5. **Dedicated Hosts**

💡 **"Your own physical server."**

* A **full physical EC2 server** dedicated to you.
* Allows **BYOL (Bring Your Own License)** for per-socket/per-core licensed software (e.g., Oracle, SQL Server).
* Most expensive option.
* Purchase options: **On-Demand** or **Reserved (1 or 3 years)**.

✅ **Use Cases**

* Regulatory/compliance requirements.
* Software with strict licensing (e.g., Windows Server Datacenter licensed per socket).

🔖 **Example**:
A bank must meet compliance by using **non-shared physical infrastructure** for security audits.

---

## 🔹 6. **Dedicated Instances**

💡 **"Dedicated hardware, but AWS controls placement."**

* Instances run on **hardware not shared with other customers**.
* Unlike Dedicated Hosts → you don’t control which hardware.
* Can move when stopped/started.

✅ **Use Cases**

* Companies needing **isolation** for compliance, but no strict hardware licensing requirements.

---

## 🔹 7. **Capacity Reservations**

💡 **"Reserve capacity in an AZ, anytime."**

* Guarantee that you’ll always have capacity in a specific AZ.
* No commitment; can create and cancel anytime.
* Billed at **On-Demand rates** (no discount).
* Can combine with RIs or Savings Plans to get discounts.

✅ **Use Cases**

* Short-term workloads that must run in a specific AZ.
* Disaster recovery (DR) planning — always ensure capacity available.

🔖 **Example**:
Running a **DR drill** every quarter → reserve capacity in advance in `us-east-1a`.

---

# 📊 Quick Comparison (Exam-Friendly)

| Option                   | Discount    | Commitment | Reliability | Best For                               |
| ------------------------ | ----------- | ---------- | ----------- | -------------------------------------- |
| **On-Demand**            | ❌ none      | None       | ✅ High      | Short/unpredictable workloads          |
| **Reserved**             | ✅ up to 72% | 1–3 years  | ✅ High      | Steady workloads (DBs)                 |
| **Convertible RI**       | ✅ up to 66% | 1–3 years  | ✅ High      | Long-term, flexible workloads          |
| **Savings Plans**        | ✅ up to 72% | 1–3 years  | ✅ High      | Flexible workloads, EC2+Fargate+Lambda |
| **Spot**                 | ✅ up to 90% | None       | ❌ Low       | Fault-tolerant, batch jobs             |
| **Dedicated Host**       | ❌ Expensive | 1–3 years  | ✅ High      | BYOL, compliance                       |
| **Dedicated Instance**   | ❌ Expensive | None       | ✅ High      | Isolation without placement control    |
| **Capacity Reservation** | ❌ Expensive | None       | ✅ High      | Capacity guarantee in AZ               |

---

✅ **Exam Tip (SAA-C03)**:

* If you see **“steady workloads”** → Reserved.
* If you see **“short, unpredictable workloads”** → On-Demand.
* If you see **“flexible compute across EC2, Fargate, Lambda”** → Savings Plans.
* If you see **“cost optimization, fault-tolerant jobs”** → Spot.
* If you see **“compliance or BYOL”** → Dedicated Host.
* If you see **“must guarantee AZ capacity”** → Capacity Reservation.