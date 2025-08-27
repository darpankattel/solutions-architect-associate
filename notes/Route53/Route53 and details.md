# Amazon Route 53 - Complete Study Guide

## What is DNS?
The Domain Name System (DNS) is the backbone of the Internet that translates human-friendly hostnames into machine-readable IP addresses.

**Example:** `www.google.com` → `172.217.18.36`

DNS uses a hierarchical naming structure:
- `.com` (Top Level Domain)
- `example.com` (Second Level Domain)
- `www.example.com` (Subdomain)
- `api.example.com` (Another subdomain)

## Key DNS Terminologies

- **Domain Registrar**: Where you buy domain names (Amazon Route 53, GoDaddy, etc.)
- **DNS Records**: Instructions that define how traffic should be routed (A, AAAA, CNAME, NS)
- **Zone File**: Contains all DNS records for a domain
- **Name Server**: Resolves DNS queries (can be Authoritative or Non-Authoritative)
- **Top Level Domain (TLD)**: `.com`, `.us`, `.in`, `.gov`, `.org`
- **Second Level Domain (SLD)**: `amazon.com`, `google.com`

## Route 53 Records Structure

Each DNS record contains:
- **Domain/subdomain Name**: e.g., `example.com`
- **Record Type**: e.g., A or AAAA
- **Value**: e.g., `12.34.56.78`
- **Routing Policy**: How Route 53 responds to queries
- **TTL**: Amount of time the record is cached at DNS Resolvers

## The 4 Essential DNS Record Types

### 1. A Record
- **Purpose**: Maps a hostname to IPv4 address
- **Creates Redirect**: No - direct resolution
- **Example**: `www.example.com` → `192.168.1.1`
- **Use Case**: Standard web hosting, most common record type

### 2. AAAA Record
- **Purpose**: Maps a hostname to IPv6 address
- **Creates Redirect**: No - direct resolution
- **Example**: `www.example.com` → `2001:0db8:85a3:0000:0000:8a2e:0370:7334`
- **Use Case**: IPv6 support for modern applications

### 3. CNAME Record
- **Purpose**: Maps a hostname to another hostname
- **Creates Redirect**: Yes - canonical name redirect
- **Example**: `blog.example.com` → `www.example.com`
- **Limitations**: 
  - Cannot be used for root domain (Zone Apex) - you CAN'T create CNAME for `example.com`
  - CAN be used for subdomains like `www.example.com`
  - Target must have an A or AAAA record
- **Use Case**: Subdomain redirection, CDN setup

### 4. NS Record
- **Purpose**: Defines Name Servers for the Hosted Zone
- **Creates Redirect**: No - defines authority
- **Example**: `example.com` → `ns-123.awsdns-12.com`
- **Use Case**: Controls how traffic is routed for a domain

## Record Type Comparison Summary

| Record | Redirect? | Root Domain? | Target Type | Example |
|--------|-----------|--------------|-------------|---------|
| A | No | Yes | IPv4 Address | `example.com` → `1.2.3.4` |
| AAAA | No | Yes | IPv6 Address | `example.com` → `2001:db8::1` |
| CNAME | Yes | **NO** | Hostname | `www.example.com` → `example.com` |
| NS | No | Yes | Name Server | `example.com` → `ns1.awsdns.com` |

## CNAME vs Alias vs A Records - Deep Dive

### Understanding the Record Types

**Important Clarification**: Alias is NOT a separate DNS record type. It's an AWS-specific **parameter/flag** that can be applied to A and AAAA records to give them special capabilities.

### A Record (Standard)
```
Type: A
Name: www.example.com
Value: 192.0.2.1
TTL: 300
Alias: No
```
- **Purpose**: Direct hostname to IP mapping
- **Root Domain**: ✅ Supported
- **Subdomain**: ✅ Supported
- **Target**: IPv4 address only
- **Redirect**: No (direct resolution)
- **Cost**: Standard DNS query charges

### A Record with Alias (AWS Extension)
```
Type: A
Name: example.com
Value: my-alb-123456789.us-east-1.elb.amazonaws.com
TTL: N/A (Auto-managed)
Alias: Yes
```
- **Purpose**: Hostname to AWS resource mapping
- **Root Domain**: ✅ Supported (KEY ADVANTAGE)
- **Subdomain**: ✅ Supported
- **Target**: AWS resources only
- **Redirect**: No (direct resolution, but dynamic IP)
- **Cost**: Free of charge
- **Special**: Auto-updates when AWS resource IP changes

### CNAME Record (Standard DNS)
```
Type: CNAME
Name: www.example.com
Value: example.com
TTL: 300
Alias: No
```
- **Purpose**: Hostname to hostname mapping (canonical name)
- **Root Domain**: ❌ NOT Supported (Zone Apex restriction)
- **Subdomain**: ✅ Supported
- **Target**: Any hostname (must resolve to A/AAAA)
- **Redirect**: Yes (creates a chain of lookups)
- **Cost**: Standard DNS query charges

### Why CNAME Cannot Be Used for Root Domains

**The Zone Apex Problem:**

```
❌ INVALID - This will NOT work:
Type: CNAME
Name: example.com
Value: lb-123.amazonaws.com

Why it fails:
- Root domain (example.com) needs SOA and NS records
- CNAME cannot coexist with other record types
- DNS specification RFC 1034 forbids this
```

**Real-World Example of the Problem:**
```
Domain: mystore.com

Required Records for Root Domain:
- SOA record (Start of Authority)
- NS records (Name Servers)
- MX record (Mail Exchange) - if you want email

If you try to add CNAME:
mystore.com CNAME → lb-123.amazonaws.com

Result: CONFLICT! 
- CNAME says "mystore.com is really lb-123.amazonaws.com"
- But NS says "mystore.com has these name servers"
- MX says "mystore.com has this mail server"

DNS doesn't know which one to believe!
```

### Practical Examples Comparison

#### Scenario 1: E-commerce Website

**❌ Wrong Way (CNAME on root):**
```
# This will FAIL
Type: CNAME
Name: mystore.com
Value: mystore-alb-123.us-east-1.elb.amazonaws.com
Result: DNS Error - Cannot create CNAME for Zone Apex
```

**✅ Correct Way (Alias A record):**
```
Type: A
Name: mystore.com
Value: mystore-alb-123.us-east-1.elb.amazonaws.com
Alias: Yes
Result: Works perfectly, free, auto-updates
```

**✅ Alternative (Regular A record):**
```
Type: A
Name: mystore.com
Value: 203.0.113.1
Alias: No
Result: Works, but need to update manually if IP changes
```

#### Scenario 2: Subdomain Setup

**✅ All these work for subdomains:**

```
# CNAME approach
Type: CNAME
Name: www.mystore.com
Value: mystore.com
Result: www redirects to main site

# Alias A record approach  
Type: A
Name: www.mystore.com
Value: mystore-alb-123.us-east-1.elb.amazonaws.com
Alias: Yes
Result: Direct resolution to load balancer

# Regular A record approach
Type: A  
Name: www.mystore.com
Value: 203.0.113.1
Alias: No
Result: Direct resolution to IP
```

### When to Use Each Approach

#### Use A Record (Regular) When:
- Pointing to static IP addresses
- Non-AWS resources
- Simple setups where IP won't change
- Cost is not a major concern

#### Use A Record with Alias When:
- Pointing to AWS resources (ELB, CloudFront, etc.)
- Need root domain support
- Want automatic IP updates
- Want to save on DNS query costs
- AWS-only infrastructure

#### Use CNAME When:
- Creating subdomain redirects
- Pointing to non-AWS hostnames
- Need hostname-to-hostname mapping
- Working with third-party services

### Complete Configuration Examples

#### Example 1: Blog with CDN
```
# Root domain - Must use Alias A record
Type: A
Name: myblog.com
Value: d123456789.cloudfront.net
Alias: Yes
TTL: Auto

# WWW subdomain - Can use CNAME
Type: CNAME
Name: www.myblog.com  
Value: myblog.com
TTL: 300

# Admin subdomain - Direct to server
Type: A
Name: admin.myblog.com
Value: 203.0.113.50
Alias: No
TTL: 300
```

#### Example 2: Multi-Region Application
```
# Root domain - Alias to Application Load Balancer
Type: A
Name: app.company.com
Value: company-alb-prod-123.us-east-1.elb.amazonaws.com
Alias: Yes
Routing Policy: Latency-based
Region: us-east-1

# API subdomain - CNAME to main app
Type: CNAME
Name: api.app.company.com
Value: app.company.com
TTL: 300

# Static assets - Alias to CloudFront
Type: A
Name: static.company.com
Value: d987654321.cloudfront.net
Alias: Yes
```

## Email Configuration for Domains in AWS

### Understanding Email DNS Records

#### MX (Mail Exchange) Records
```
Type: MX
Name: company.com
Value: 10 mail.company.com
TTL: 3600

Components:
- Priority: 10 (lower = higher priority)
- Mail Server: mail.company.com
```

#### Complete Email Setup Example

```
Domain: techstartup.com

# MX Records (Mail routing)
Type: MX
Name: techstartup.com
Value: 10 mail1.techstartup.com
TTL: 3600

Type: MX  
Name: techstartup.com
Value: 20 mail2.techstartup.com  
TTL: 3600

# A Records for mail servers
Type: A
Name: mail1.techstartup.com
Value: 203.0.113.100
TTL: 3600

Type: A
Name: mail2.techstartup.com
Value: 203.0.113.101
TTL: 3600

# SPF Record (Sender Policy Framework)
Type: TXT
Name: techstartup.com
Value: "v=spf1 mx a:mail.techstartup.com include:_spf.google.com ~all"
TTL: 3600

# DKIM Record (DomainKeys Identified Mail)
Type: TXT
Name: selector1._domainkey.techstartup.com
Value: "k=rsa; p=MIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQC..."
TTL: 3600

# DMARC Record (Domain-based Message Authentication)
Type: TXT
Name: _dmarc.techstartup.com
Value: "v=DMARC1; p=quarantine; rua=mailto:dmarc@techstartup.com"
TTL: 3600
```

### AWS Email Solutions

#### Option 1: Amazon SES (Simple Email Service)
**When to use**: Transactional emails, newsletters, marketing emails

**Configuration**:
```
# No MX record needed for sending
# Only TXT records for verification and authentication

# Domain verification
Type: TXT
Name: _amazonses.company.com
Value: "verification-token-from-ses"
TTL: 1800

# DKIM for SES
Type: CNAME
Name: token1._domainkey.company.com
Value: token1.dkim.amazonses.com
TTL: 1800
```

**Pros**:
- Managed service
- High deliverability
- Pay-per-use
- Built-in bounce/complaint handling

**Cons**:
- Sending only (no receiving without additional setup)
- Not suitable for traditional email hosting

#### Option 2: Amazon WorkMail
**When to use**: Corporate email hosting (like Exchange/Gmail)

**Configuration**:
```
# MX Record
Type: MX
Name: company.com
Value: 10 inbound-smtp.us-east-1.amazonaws.com
TTL: 3600

# Autodiscover for Outlook
Type: CNAME
Name: autodiscover.company.com
Value: autodiscover.mail.us-east-1.awsapps.com
TTL: 300

# SPF Record
Type: TXT
Name: company.com
Value: "v=spf1 include:amazonses.com ~all"
TTL: 300
```

**Pros**:
- Full email hosting solution
- Integrated with AWS ecosystem
- Outlook/mobile app support
- Calendar and contacts

**Cons**:
- More expensive than alternatives
- Per-user licensing

#### Option 3: Third-Party Email Hosting
**Popular options**: Google Workspace, Microsoft 365, Zoho Mail

**Google Workspace Example**:
```
# MX Records for Google
Type: MX
Name: company.com
Value: 1 smtp.google.com
TTL: 3600

Type: MX
Name: company.com
Value: 5 gmail-smtp-in.l.google.com
TTL: 3600

# SPF Record
Type: TXT
Name: company.com
Value: "v=spf1 include:_spf.google.com ~all"
TTL: 300

# DKIM (provided by Google)
Type: TXT
Name: google._domainkey.company.com
Value: "v=DKIM1; k=rsa; p=Google-provided-key"
TTL: 300
```

#### Option 4: Self-Hosted Mail Server (Advanced)
**When required**: 
- Full control over email infrastructure
- Compliance requirements
- Cost optimization for large volumes
- Integration with existing systems

**Basic Setup Requirements**:
```
# MX Record pointing to your server
Type: MX
Name: company.com
Value: 10 mail.company.com
TTL: 3600

# A Record for mail server
Type: A
Name: mail.company.com
Value: 203.0.113.200
TTL: 3600

# Reverse DNS (PTR) - configured at ISP/hosting provider
PTR Record: 203.0.113.200 → mail.company.com

# SPF Record
Type: TXT
Name: company.com
Value: "v=spf1 mx a:mail.company.com -all"
TTL: 300

# DKIM Record (generated by mail server)
Type: TXT
Name: mail._domainkey.company.com
Value: "v=DKIM1; k=rsa; p=your-generated-public-key"
TTL: 300
```

**Popular Self-Hosted Solutions**:
- **Postfix + Dovecot**: Most common Linux setup
- **Mail-in-a-Box**: Easy deployment solution
- **iRedMail**: Complete email server solution
- **Zimbra**: Enterprise-grade email platform

### Email Security Records Explained

#### SPF (Sender Policy Framework)
**Purpose**: Prevents email spoofing by specifying which servers can send email for your domain

```
"v=spf1 mx include:_spf.google.com ~all"

Breakdown:
- v=spf1: SPF version 1
- mx: Allow MX record servers to send
- include:_spf.google.com: Include Google's SPF record
- ~all: Soft fail for all other servers
```

#### DKIM (DomainKeys Identified Mail)
**Purpose**: Cryptographic signature to verify email authenticity

```
Type: TXT
Name: selector1._domainkey.company.com
Value: "k=rsa; p=public-key-here"

Components:
- selector1: Key selector (you can have multiple)
- k=rsa: Key type
- p=: Public key for verification
```

#### DMARC (Domain-based Message Authentication)
**Purpose**: Policy for handling emails that fail SPF/DKIM checks

```
"v=DMARC1; p=quarantine; rua=mailto:reports@company.com"

Breakdown:
- v=DMARC1: DMARC version 1
- p=quarantine: Policy (none/quarantine/reject)
- rua=: Address for aggregate reports
```

### Choosing the Right Email Solution

| Solution | Best For | Cost | Complexity | Features |
|----------|----------|------|------------|----------|
| Amazon SES | Transactional emails | Low | Low | Sending only |
| Amazon WorkMail | Small-medium business | Medium | Low | Full email hosting |
| Google Workspace | General business use | Medium | Low | Full suite + collaboration |
| Microsoft 365 | Enterprise | Medium-High | Low | Full suite + Office apps |
| Self-hosted | High volume/compliance | Variable | High | Full control |

### Email Setup Checklist

1. **Choose email solution** based on requirements
2. **Configure MX records** for receiving email
3. **Set up SPF record** to prevent spoofing
4. **Configure DKIM** for email authentication
5. **Implement DMARC** for policy enforcement
6. **Test email delivery** and receiving
7. **Monitor deliverability** and reputation
8. **Set up monitoring** for email server health (if self-hosted)

## Hosted Zones

### Public Hosted Zones
- Contains records for public domain names
- Routes traffic on the Internet
- Example: `application1.mypublicdomain.com`

### Private Hosted Zones
- Routes traffic within VPCs
- Private domain names only
- Example: `application1.company.internal`

**Cost**: $0.50 per month per hosted zone

## TTL (Time To Live)

### High TTL (24 hours):
- **Pros**: Less traffic on Route 53, lower costs
- **Cons**: Possibly outdated records, slower changes

### Low TTL (60 seconds):
- **Pros**: Records updated quickly, easy to change
- **Cons**: More traffic on Route 53, higher costs

**Note**: TTL is mandatory for all DNS records except Alias records

## Route 53 Routing Policies

### 1. Simple Routing
- Routes traffic to a single resource
- Can specify multiple values (random selection by client)
- Cannot be associated with Health Checks
- Use Case: Basic single-server setup

### 2. Weighted Routing
- Controls percentage of requests to each resource
- Records must have same name and type
- Can be associated with Health Checks
- Use Cases: Load balancing between regions, A/B testing
- **Tip**: Weight of 0 stops traffic to a resource

### 3. Latency-Based Routing
- Redirects to resource with lowest latency
- Based on traffic between users and AWS Regions
- Can be associated with Health Checks
- Use Case: Performance optimization for global users

### 4. Failover Routing (Active-Passive)
- Primary and secondary resources
- Automatic failover when primary fails
- Requires Health Checks
- Use Case: Disaster recovery

### 5. Geolocation Routing
- Based on user's geographic location
- Specify by Continent, Country, or US State
- Most precise location takes precedence
- Should create "Default" record for unmatched locations
- Use Cases: Website localization, content restriction

### 6. Geoproximity Routing
- Routes based on geographic location of users AND resources
- Uses bias values to shift traffic (-99 to +99)
- Requires Route 53 Traffic Flow
- Use Case: Fine-tuned geographic traffic control

### 7. Multi-Value Routing
- Returns multiple healthy resources (up to 8)
- Can be associated with Health Checks
- Not a substitute for ELB
- Use Case: Simple load distribution

### 8. IP-Based Routing
- Routes based on client IP addresses
- Provide CIDR blocks and corresponding endpoints
- Use Cases: ISP-specific routing, cost optimization

## Health Checks

### Types of Health Checks:
1. **Monitor an Endpoint**: Direct endpoint monitoring
2. **Monitor Other Health Checks**: Calculated Health Checks
3. **Monitor CloudWatch Alarms**: For private resources

### Endpoint Health Checks:
- 15 global health checkers
- Default threshold: 3 healthy/unhealthy
- Default interval: 30 seconds (can be 10 seconds for higher cost)
- Protocols: HTTP, HTTPS, TCP
- Healthy if >18% of checkers report healthy
- Success criteria: 2xx and 3xx status codes

### Calculated Health Checks:
- Combine multiple health checks (up to 256)
- Use OR, AND, or NOT logic
- Useful for maintenance scenarios

### Private Resource Health Checks:
- Route 53 health checkers are outside VPC
- Use CloudWatch Metrics and Alarms
- Create Health Check that monitors the alarm

## Alias Record Targets

Route 53 Alias records can point to:
- Elastic Load Balancers
- CloudFront Distributions
- API Gateway
- Elastic Beanstalk environments
- S3 Websites
- VPC Interface Endpoints
- Global Accelerator
- Other Route 53 records in same hosted zone

**Important**: Cannot create Alias record for EC2 DNS name

## Domain Registrar vs DNS Service

**Key Concept**: Domain Registrar ≠ DNS Service

### Scenario: GoDaddy + Route 53
1. Buy domain from GoDaddy (Domain Registrar)
2. Use Route 53 for DNS management (DNS Service)
3. Update NS records in GoDaddy to point to Route 53 name servers

### Steps to use Route 53 with 3rd party registrar:
1. Create Hosted Zone in Route 53
2. Update NS Records on 3rd party website to use Route 53 Name Servers

## What is a Name Server?

A **Name Server** is a server that stores DNS zone files and responds to DNS queries. It's essentially the "phone book" of the internet that tells other systems where to find your domain.

### When do you need Name Servers?

1. **When you register a domain**: Every domain must have at least 2 name servers
2. **When you change DNS providers**: Update NS records to point to new provider
3. **When setting up subdomains**: Delegate authority to different name servers
4. **For load distribution**: Spread DNS query load across multiple servers

### Name Server Example:
```
Domain: example.com
Name Servers:
- ns-123.awsdns-12.com
- ns-456.awsdns-34.net
- ns-789.awsdns-56.org
- ns-012.awsdns-78.co.uk
```

When someone queries `www.example.com`, their DNS resolver contacts these name servers to get the actual IP address.

## DNS Record Configuration Examples

### Basic Website Setup
```
Domain: mycompany.com

A Record:
Name: mycompany.com
Type: A
Value: 203.0.113.12
TTL: 300

A Record:
Name: www.mycompany.com
Type: A
Value: 203.0.113.12
TTL: 300

MX Record:
Name: mycompany.com
Type: MX
Value: 10 mail.mycompany.com
TTL: 3600
```

### E-commerce with CDN
```
Domain: shop.example.com

CNAME Record:
Name: www.shop.example.com
Type: CNAME
Value: shop.example.com
TTL: 300

Alias Record:
Name: shop.example.com
Type: A (Alias)
Value: d1234567890.cloudfront.net
TTL: Auto (60 seconds)

CNAME Record:
Name: cdn.shop.example.com
Type: CNAME
Value: d1234567890.cloudfront.net
TTL: 86400
```

### API and Microservices Setup
```
Domain: api.myapp.com

Alias Record:
Name: api.myapp.com
Type: A (Alias)
Value: my-api-alb-123456789.us-east-1.elb.amazonaws.com
TTL: Auto

CNAME Records:
Name: users.api.myapp.com
Type: CNAME
Value: api.myapp.com
TTL: 300

Name: orders.api.myapp.com
Type: CNAME
Value: api.myapp.com
TTL: 300

Name: payments.api.myapp.com
Type: CNAME
Value: api.myapp.com
TTL: 300
```

### Development/Staging Environment
```
Domain: myapp.com

A Record (Production):
Name: myapp.com
Type: A
Value: 203.0.113.50
TTL: 3600

A Record (Staging):
Name: staging.myapp.com
Type: A
Value: 203.0.113.51
TTL: 300

A Record (Development):
Name: dev.myapp.com
Type: A
Value: 203.0.113.52
TTL: 60
```

### Email Configuration
```
Domain: company.com

MX Records:
Name: company.com
Type: MX
Value: 10 mail1.company.com
TTL: 3600

Name: company.com
Type: MX
Value: 20 mail2.company.com
TTL: 3600

A Records for Mail Servers:
Name: mail1.company.com
Type: A
Value: 203.0.113.100
TTL: 3600

Name: mail2.company.com
Type: A
Value: 203.0.113.101
TTL: 3600

TXT Record (SPF):
Name: company.com
Type: TXT
Value: "v=spf1 mx a:mail.company.com ~all"
TTL: 3600
```

## Routing Policy Usage Examples

### 1. Simple Routing - Basic Blog
```
Scenario: Personal blog hosted on single EC2 instance

Configuration:
Name: blog.johndoe.com
Type: A
Value: 203.0.113.25
Routing Policy: Simple
TTL: 300

Use Case: Simple, single-server websites with no redundancy needs
```

### 2. Weighted Routing - Blue/Green Deployment
```
Scenario: Rolling out new application version gradually

Blue Environment (Current):
Name: app.company.com
Type: A
Value: 203.0.113.10
Routing Policy: Weighted
Weight: 80
Health Check: Enabled

Green Environment (New):
Name: app.company.com
Type: A
Value: 203.0.113.20
Routing Policy: Weighted
Weight: 20
Health Check: Enabled

Result: 80% traffic goes to current version, 20% to new version
```

### 3. Latency-Based Routing - Global Application
```
Scenario: Global SaaS application with regional servers

US East Configuration:
Name: app.globaltech.com
Type: A
Value: 203.0.113.30
Routing Policy: Latency-based
Region: us-east-1
Health Check: Enabled

Europe Configuration:
Name: app.globaltech.com
Type: A
Value: 198.51.100.40
Routing Policy: Latency-based
Region: eu-west-1
Health Check: Enabled

Asia Configuration:
Name: app.globaltech.com
Type: A
Value: 192.0.2.50
Routing Policy: Latency-based
Region: ap-southeast-1
Health Check: Enabled

Result: Users automatically routed to closest server
```

### 4. Failover Routing - Disaster Recovery
```
Scenario: E-commerce site with backup server

Primary (Active):
Name: store.retailer.com
Type: A
Value: 203.0.113.60
Routing Policy: Failover
Failover Record Type: Primary
Health Check: Required

Secondary (Passive):
Name: store.retailer.com
Type: A
Value: 198.51.100.70
Routing Policy: Failover
Failover Record Type: Secondary
Health Check: Optional

Result: Traffic goes to secondary only when primary fails
```

### 5. Geolocation Routing - Content Localization
```
Scenario: News website with regional content

US Users:
Name: news.global.com
Type: A
Value: 203.0.113.80
Routing Policy: Geolocation
Location: North America
Health Check: Enabled

European Users:
Name: news.global.com
Type: A
Value: 198.51.100.90
Routing Policy: Geolocation
Location: Europe
Health Check: Enabled

Default (Rest of World):
Name: news.global.com
Type: A
Value: 192.0.2.100
Routing Policy: Geolocation
Location: Default
Health Check: Enabled

Result: Users see localized content based on their location
```

### 6. Geoproximity Routing - Traffic Shifting
```
Scenario: Gaming company wants to shift traffic between data centers

US West Data Center:
Name: game.epicgames.com
Type: A
Value: 203.0.113.110
Routing Policy: Geoproximity
Coordinates: AWS Region us-west-2
Bias: +50 (expand coverage)
Health Check: Enabled

US East Data Center:
Name: game.epicgames.com
Type: A
Value: 198.51.100.120
Routing Policy: Geoproximity
Coordinates: AWS Region us-east-1
Bias: -30 (shrink coverage)
Health Check: Enabled

Result: More traffic directed to West Coast data center
```

### 7. Multi-Value Routing - Simple Load Distribution
```
Scenario: API service with multiple backend servers

Server 1:
Name: api.service.com
Type: A
Value: 203.0.113.130
Routing Policy: Multivalue
Health Check: Enabled

Server 2:
Name: api.service.com
Type: A
Value: 198.51.100.140
Routing Policy: Multivalue
Health Check: Enabled

Server 3:
Name: api.service.com
Type: A
Value: 192.0.2.150
Routing Policy: Multivalue
Health Check: Enabled

Result: DNS returns multiple IPs, client chooses one (up to 8 healthy records)
```

### 8. IP-Based Routing - ISP Optimization
```
Scenario: Video streaming service optimizing for specific ISPs

Comcast Users:
Name: stream.video.com
Type: A
Value: 203.0.113.160
Routing Policy: IP-based
CIDR: 69.252.0.0/16 (Comcast IP range)
Health Check: Enabled

Verizon Users:
Name: stream.video.com
Type: A
Value: 198.51.100.170
Routing Policy: IP-based
CIDR: 71.242.0.0/16 (Verizon IP range)
Health Check: Enabled

Default:
Name: stream.video.com
Type: A
Value: 192.0.2.180
Routing Policy: IP-based
CIDR: 0.0.0.0/0 (All other IPs)
Health Check: Enabled

Result: ISP-specific routing for optimized video delivery
```

## Real-World Configuration Scenarios

### Scenario 1: Startup to Enterprise Evolution
```
Stage 1 (Startup): Simple routing to single server
Stage 2 (Growth): Weighted routing for A/B testing
Stage 3 (Regional): Latency-based routing for global users
Stage 4 (Enterprise): Geolocation + Failover for compliance and reliability
```

### Scenario 2: E-commerce Black Friday Preparation
```
Normal Traffic: Latency-based routing to regional servers
High Traffic Expected: Multi-value routing to distribute load
Disaster Scenario: Failover routing to backup infrastructure
```

### Scenario 3: Global SaaS Application
```
DNS Structure:
- app.company.com (Alias to ALB)
- api.company.com (Latency-based routing)
- cdn.company.com (CNAME to CloudFront)
- admin.company.com (Geolocation - restricted regions)
```

## Exam Tips

1. **Remember CNAME limitations**: Cannot be used for root domains
2. **Alias vs CNAME**: Alias works for root domains and is free
3. **Health Checks**: Only work for public resources directly
4. **Weighted Routing**: Weight of 0 stops all traffic
5. **Geolocation vs Geoproximity**: Location-based vs location + bias-based
6. **TTL**: Not required for Alias records
7. **Failover**: Requires Health Checks
8. **Multi-Value**: Returns up to 8 healthy records, not a load balancer replacement
9. **Name Servers**: Every domain needs at least 2, used for DNS delegation
10. **Real-world scenarios**: Combine multiple routing policies for complex applications