# TCS Digital Interview — Full Review & Corrected Answers

## Pattern-level issues first (fix these habits, not just the facts)

1. **You keep restarting the question before answering** ("what is public cloud, public cloud means, sir we can pub...") — this eats your own time and makes you sound like you're stalling, even when you actually know part of the answer. Train yourself to answer in the first sentence, then add detail.
2. **5 full silences.** Not wrong answers — no answer at all. That's the biggest thing to fix. A wrong-but-attempted answer shows thinking; silence shows you never touched that topic hands-on.
3. **You memorized service *names* well but not what they *do*.** You correctly listed S3 storage classes (Standard, IA, Glacier) and storage types (file/object/block) — that's real recall. But when asked to explain *how* something works (NAT gateway, subnet/VPC relationship, backup vs DR), the answers get reversed or muddled. This is the exact "watched videos, no grip" problem — you can recognize terms, you can't yet reason through them.
4. **A few answers are factually backwards**, not just vague — these are the most important to fix because saying the opposite of the truth is worse than saying "I'm not sure." Flagged clearly below.

---

## Section 1: Cloud Fundamentals

| Question | What you said | Issue | Correct answer |
|---|---|---|---|
| What is cloud computing? | A virtual platform giving compute/storage/network over the internet | Mostly fine, a bit vague | On-demand delivery of IT resources (compute, storage, databases, networking) over the internet, with pay-as-you-go pricing, instead of owning physical hardware. |
| Why do companies use cloud? | Cost efficiency, pay-as-you-go | Correct but shallow | Also mention: no upfront hardware cost (CapEx→OpEx), scalability, faster deployment, global reach, reduced maintenance burden. |
| What is pay-as-you-go? | Rambling, restated the question, didn't land a clear definition | Needs a crisp one-liner | You pay only for the resources you actually consume (e.g., compute hours, GB stored), with no fixed long-term contract — like an electricity bill. |
| Public / Private / Hybrid cloud | Trailed off on "public cloud" | Incomplete | **Public**: shared infrastructure, owned by provider (AWS/Azure), open to any customer. **Private**: dedicated infra for one organization, more control/security. **Hybrid**: mix of both — sensitive data on-prem/private, scalable workloads on public cloud. |
| What is virtualization? | "It will be on computer over the internet... it is a server like... it on the cloud" — unclear | Confused definition | Virtualization = using software (a hypervisor) to create multiple virtual machines on one physical server, each with its own OS, sharing the underlying hardware. It's the *technology that makes cloud possible*, not "the cloud" itself. |
| Docker / containerization | Trailed off | Incomplete | Containerization packages an application with everything it needs (code, libraries, dependencies) into a single lightweight, portable unit that runs consistently across environments. Docker is the most popular containerization platform. |
| Docker vs VM | "Docker is lightweight, doesn't control on OS" | Right idea, badly phrased | VMs virtualize hardware — each VM has its own full OS, making them heavier and slower to start. Containers virtualize at the OS level — they share the host's OS kernel, so they're lightweight, start in seconds, and use fewer resources. |

---

## Section 2: Scaling & Load Balancing — this section needs real rework

| Question | What you said | Issue | Correct answer |
|---|---|---|---|
| Elasticity / Scalability | "Number of people hitting your site means amount of scalability will increase" | Backwards phrasing — you described *load*, not *scalability* | **Scalability** = the system's *ability* to handle increased load by adding resources. **Elasticity** = the system automatically adding/removing resources in real time as demand changes (specific to cloud). |
| Types of scaling | Only said "auto scaling," then confused horizontal scaling with Availability Zones | **This mixed two unrelated concepts** | **Vertical scaling** (scale up) = increasing the power of a single server (more CPU/RAM). **Horizontal scaling** (scale out) = adding more servers/instances to share the load. AZs are about fault tolerance/location, not scaling type — don't mix them. |
| Auto scaling | "It will automatically increase or decrease" | Correct, just needs one more sentence | AWS Auto Scaling automatically adds or removes EC2 instances based on demand (e.g., CPU utilization thresholds), so you don't manually provision servers for traffic spikes. |
| Load balancing | Roughly right but vague | Missing the mechanism | A Load Balancer (e.g., AWS ELB) distributes incoming traffic across multiple servers/instances (often across multiple AZs) so no single server gets overwhelmed, and traffic reroutes automatically if one instance fails. |

---

## Section 3: Storage — one real factual error here, important to fix

| Question | What you said | Issue | Correct answer |
|---|---|---|---|
| Types of storage | File, Object, Block | **Correct** — good recall | Keep this, it's solid. |
| What is S3? | "S3 sir, in virtualization file storage system" — later also called it "block storage" | **Factually wrong, twice** | S3 (Simple Storage Service) is AWS's **object storage** service — used for files, images, backups, static website hosting. It is NOT block storage (that's EBS) and NOT specifically tied to "virtualization." |
| S3 pricing tiers | Named S3 Standard, Standard-IA, Glacier correctly | Good recall of names | S3 Standard = frequently accessed data. Standard-IA = infrequently accessed but needs fast retrieval when needed. Glacier = archival, cheap storage, slow retrieval (minutes to hours). |
| S3 transfer pricing | "Importing is free, exporting is charged" — right idea, wrong words | Terminology issue | Correct term: **data transfer IN (upload) to AWS is free; data transfer OUT (download/egress) is charged.** Use "transfer in/out," not "importing/exporting" — interviewers notice correct terminology. |
| Backup | "Whenever availability is crashed, we use another AZ" | This describes **failover**, not backup | Backup = a stored copy of your data (e.g., an S3 snapshot or RDS snapshot) that you can restore from if data is lost or corrupted. |
| Backup vs Disaster Recovery | "Backup stores in files, DR stores in AZ" | Confused/incorrect distinction | **Backup** = copies of data for restoring lost/corrupted data. **Disaster Recovery (DR)** = a complete strategy/plan to restore your entire system's operation after a major failure (region outage, disaster) — includes backups, but also replication, failover procedures, and defined recovery time targets (RTO/RPO). DR is the broader plan; backup is one component of it. |

---

## Section 4: Networking — multiple reversed/confused answers, prioritize this section

| Question | What you said | Issue | Correct answer |
|---|---|---|---|
| What is a subnet? | "Creating a virtual server automatically gives a private address and creates a VPC" | **Logic reversed** — subnet doesn't create a VPC | It's the other way around: you create a **VPC** first (your private network space in AWS), then divide it into **subnets** (smaller IP ranges within that VPC, often one per Availability Zone) — public subnets for internet-facing resources, private subnets for internal resources. |
| VPN Gateway vs Internet Gateway | Vague, conflated the two | Different services | **Internet Gateway**: connects your VPC to the public internet. **VPN Gateway**: creates a secure encrypted connection between your VPC and an on-premises network (e.g., your company's office network). Don't use these interchangeably. |
| NAT Gateway | "It blocks outbound traffic and allows inbound" | **This is backwards — a real factual error, important to fix** | NAT Gateway does the opposite: it **allows outbound** internet access for resources in a private subnet (e.g., to download updates) while **blocking unsolicited inbound** traffic from the internet to those private resources. |
| NAT vs WAF | Conflated the two | Different layers/purposes entirely | **NAT Gateway**: network-layer, manages outbound/inbound traffic direction for private subnets. **WAF (Web Application Firewall)**: application-layer, filters malicious HTTP traffic (SQL injection, XSS, bad bots) to protect web applications. These solve completely different problems — don't group them together in an answer. |
| DNS | "Domain Network Service" | **Wrong expansion** | DNS = **Domain Name System**. It translates human-readable domain names (google.com) into IP addresses so browsers can locate servers. |
| Public IP address | "It will allow all the traffic" | Incorrect — that's a security group's job, not the IP's | A public IP is simply an internet-routable address assigned to a resource so it can be reached from the internet. Whether traffic is *allowed* is controlled separately by Security Groups/NACLs, not by the IP address itself. |
| Availability Zone | Very minimal ("3 AZ, a b c") | Too shallow | An AZ is one or more physically separate data centers within an AWS Region, each with independent power, cooling, and networking — so a failure in one AZ doesn't take down another. Spreading resources across AZs is how you achieve high availability. |
| AWS Region | Reasonably OK, but "edge computing for India" is a mixed-up detail | Minor mix-up | A Region is a geographic area with multiple AZs; pricing and available services vary by region. Faster access via edge locations is a **CloudFront (CDN)** concept, not a general "region" feature — keep these separate in your answer. |

---

## Section 5: Security & IAM — this is where most of the silences happened

| Question | What you said | Issue | Correct answer |
|---|---|---|---|
| IAM | HR analogy — decent attempt but imprecise | Partial credit, needs precision | IAM (Identity and Access Management) lets you create users/roles and attach **policies** that define exactly what AWS resources/actions they're allowed to access — following least privilege (see below). |
| IAM roles | **Silence** | Gap to fill | A role is a set of permissions that can be *assumed temporarily* by a user, application, or AWS service (not tied permanently to one identity like a user) — e.g., an EC2 instance assuming a role to access S3 without hardcoded credentials. |
| IAM groups | Not clearly answered | Gap to fill | A group is a collection of IAM users — you attach a policy once to the group, and every user in it inherits those permissions, instead of setting permissions per-user. |
| Least privilege | **Silence** | Important gap | Give a user/role the *minimum* permissions needed to do their job — nothing more. Reduces security risk if credentials are ever compromised. |
| MFA | Jumped to talking about IAM groups instead of answering | Didn't actually answer | MFA (Multi-Factor Authentication) requires a second verification step beyond password (e.g., a code from an authenticator app) before granting access — protects accounts even if the password is leaked. |
| Encryption | "Hide the data... send to user directly" | Too vague | Encryption converts readable data into unreadable ciphertext using an algorithm and key, so that only someone with the correct decryption key can read it — protects data at rest (stored) and in transit (network). |
| AWS Artifact | Skipped/not answered | Gap to fill | AWS Artifact is a self-service portal for accessing AWS's compliance reports and agreements (e.g., ISO certifications, SOC reports) — used for audits, not a compute/storage service. |
| Where are keys stored? | Vague non-answer | Gap to fill | **AWS KMS** (Key Management Service) manages encryption keys. **Secrets Manager** stores and rotates credentials/API keys/passwords securely. |
| AWS Glue | **Silence** | Gap to fill | AWS Glue is a serverless data integration/ETL (Extract, Transform, Load) service — used to prepare and move data between data stores for analytics. |
| AWS Cluster services | **Silence** | Gap to fill | Likely referring to ECS (Elastic Container Service) or EKS (Elastic Kubernetes Service) — managed services for running and orchestrating containers in a cluster. Worth learning both by name. |

---

## Section 6: Compute & Service Models

| Question | What you said | Issue | Correct answer |
|---|---|---|---|
| EC2 | "Elastic Compute Service... virtual service where we create servers" | Minor naming slip, concept fine | EC2 = **Elastic Compute Cloud** — resizable virtual servers (instances) in the cloud. Keep the concept, fix the name. |
| Lambda | "Serverless service... through JSON" | Confused, irrelevant detail (JSON) | Lambda is a **serverless compute service** — you upload code, and AWS runs it automatically in response to triggers (e.g., a file upload to S3, an API call), without you managing any servers. You're only charged for actual execution time. |
| IaaS / PaaS / SaaS | AWS/Azure = IaaS, Colab-like = PaaS, Zoom/Meet = SaaS | **This was actually your strongest answer** — correct structure and reasonable examples | Keep this framing, it's solid. Just tighten the definitions: IaaS = raw infrastructure (servers/storage), PaaS = platform to build/deploy apps without managing infra, SaaS = ready-to-use software. |
| "Which category does TCS fall under?" | "IaaS" | Likely wrong — this was a judgment/trick question | TCS itself isn't a cloud infrastructure provider like AWS — it's a company that *uses* cloud platforms and *delivers* IT services/solutions to clients. Depending on the specific offering being discussed, TCS's own products (like TCS BaNCS) could be argued as SaaS, or the question might be testing whether you'd say "not applicable — TCS isn't a cloud provider itself." Saying "IaaS" without reasoning was likely just a guess — for these judgment questions, thinking out loud ("TCS provides services *built on top of* cloud providers, so...") shows more than a flat one-word answer. |

---

## What to do with this document

1. Go section by section, and for every row, **explain the correct answer out loud in your own words** — don't just read it. If you can't, that's your real gap, not the ones you think you have.
2. The Networking and Security/IAM sections have the most reversed/missing answers — prioritize these two in your 30-day Linux → AWS transition.
3. Specifically build small hands-on labs for the reversed-answer items: set up a NAT Gateway and actually test outbound-vs-inbound behavior yourself; create an IAM role vs an IAM user and notice the difference; create a VPC and manually add subnets instead of memorizing the relationship.
4. Notice which answers were actually strong (storage types, IaaS/PaaS/SaaS, EC2, S3 storage classes) — you're not starting from zero, you have a real base. The next round of interview prep is about *precision and depth* on top of that base, not starting over.
