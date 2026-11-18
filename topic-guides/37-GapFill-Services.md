# Section 37: The Gap-Fill Services (small but tested)

## The idea

You've built the skyscrapers — EC2, S3, VPC, RDS. This section is the **spice rack**: dozens of small jars you'll each use once, but when the recipe calls for saffron, only saffron will do. Each of these services shows up on the exam **once or twice**, almost always as a pure **keyword-match** question. You don't need depth; you need to hear the signal word and slap the right label on it. That makes this the highest points-per-minute section you'll study — easy marks hiding in plain sight.

Read each entry as: *signal keyword → service*. That's genuinely how the questions work.

## Migration & messaging

**Amazon MQ** — managed **ActiveMQ / RabbitMQ** message broker. Signal: *"existing application uses **MQTT / AMQP / JMS** (industry-standard messaging protocols) and must migrate with **minimal rewrite**."* THE trap: for **new** applications, AWS wants **SQS/SNS** — MQ is only for lifting existing broker-based apps.

**Application Migration Service (MGN)** — the **lift-and-shift (rehost)** tool. Installs an agent on your on-prem servers, does **continuous block-level replication** into AWS, then you **cut over** to EC2 with minutes of downtime. Signal: *"migrate servers to AWS with minimal changes."*

**Elastic Disaster Recovery (DRS)** — MGN's twin, but for **DR instead of migration**: same continuous replication, but you only launch the recovery instances when disaster strikes. **RPO in seconds, RTO in minutes**, and it's cheap while idle. Signal: *"cost-effective disaster recovery for physical/virtual/cloud servers."*

**DMS + SCT** — **Database Migration Service** moves databases (and can do **ongoing replication**, source stays live during migration). If the engines differ — Oracle → Aurora — that's a **heterogeneous** migration and you add the **Schema Conversion Tool (SCT)** first. Same engine = DMS alone; **different engine = DMS + SCT**.

**The 7 Rs of migration** — one line each:

| R | Meaning |
|---|---|
| **Rehost** | Lift-and-shift, no changes (MGN) |
| **Replatform** | Lift-and-tinker (e.g., self-managed MySQL → RDS) |
| **Repurchase** | Drop it, buy SaaS instead |
| **Refactor** | Re-architect for cloud (e.g., monolith → serverless) |
| **Relocate** | Move VMware wholesale (VMware Cloud on AWS) |
| **Retain** | Leave it on-prem for now |
| **Retire** | Turn it off, nobody used it anyway |

## Certificates & network odds-and-ends

**ACM (AWS Certificate Manager)** — **free public TLS/SSL certificates** with **automatic renewal**, attachable to ALB, CloudFront, API Gateway. Two exam facts: a certificate for **CloudFront MUST live in us-east-1** (any region works for a regional ALB), and **you cannot export ACM public certs to install on an EC2 instance** — ACM only attaches to integrated services.

**Egress-only Internet Gateway** — the **IPv6 "NAT gateway."** IPv6 addresses are all public, so NAT doesn't apply; this gateway lets IPv6 instances make **outbound-only** connections while blocking inbound. Signal: *"IPv6 instances need outbound internet, no inbound."*

**Route 53 Resolver endpoints** — hybrid DNS plumbing. **Inbound endpoint**: **on-prem → VPC** DNS queries (your datacenter resolves AWS private names). **Outbound endpoint**: **VPC → on-prem** DNS queries (AWS resolves your corporate names). Remember it from the VPC's point of view: which way are the *queries coming*?

## Edge & hybrid infrastructure

| Service | One-liner | Signal keyword |
|---|---|---|
| **Outposts** | AWS-managed racks physically installed **in YOUR datacenter** | "data residency," "must stay on-premises but use AWS APIs" |
| **Local Zones** | AWS extension in a **metro city** near your users | "**single-digit millisecond** latency to users in Los Angeles" |
| **Wavelength** | AWS compute embedded in **5G telecom networks** | "5G," "mobile edge" |

## Analytics family

| Service | One-liner | Signal keyword |
|---|---|---|
| **Glue** | **Serverless ETL** (extract-transform-load) + **Data Catalog** + crawlers that auto-discover schemas | "serverless data preparation/catalog" |
| **EMR** | Managed **Hadoop/Spark** cluster; use **Spot for task nodes** to cut cost | "**Apache Spark**," "big data frameworks" |
| **MSK** | Managed **Apache Kafka** | the word "**Kafka**" — that's it |
| **Athena** | SQL queries directly on S3, pay per query (you know this one) | "query S3 with SQL" |
| **QuickSight** | **BI dashboards** and visualizations | "business dashboards for management" |
| **Lake Formation** | Build a **data lake** fast + **fine-grained (row/column) access control** | "central data lake with granular permissions" |
| **AppFlow** | Move data from **SaaS apps (Salesforce, Slack) → S3/Redshift**, no code | "Salesforce data into S3" |

THE trap: "streaming" alone → Kinesis; but "**Kafka**" named explicitly → **MSK**. AWS respects the proper noun.

## ML one-liners (pure keyword matching)

| Service | Does | Signal |
|---|---|---|
| **Rekognition** | Analyze **images/video** | face detection, content moderation |
| **Transcribe** | **Speech → text** | call transcripts, subtitles |
| **Polly** | **Text → speech** | "app reads articles aloud" |
| **Translate** | Language translation | "localize to 12 languages" |
| **Comprehend** | **NLP / sentiment** analysis | "customer review sentiment" |
| **Textract** | **Extract text + data from scanned forms/invoices** — understands tables and fields, **beyond simple OCR** | "process scanned documents/forms" |
| **Kendra** | **Intelligent document search** (natural-language answers from your docs) | "enterprise search across wikis/manuals" |
| **Personalize** | **Recommendations** engine | "customers also bought" |
| **Forecast** | Time-series **predictions** | "predict inventory demand" |
| **Lex** | **Chatbots** (the tech behind Alexa) | "conversational bot" |
| **SageMaker** | Build/train/deploy **custom ML models** | "data scientists need to train models" |

THE trap: scanned invoice/form extraction → **Textract**, not Rekognition (images ≠ documents) and not Comprehend (that's meaning, not extraction).

**Kinesis Video Streams** — **ingest/store live video streams** from cameras or devices; **Rekognition Video** analyzes video and **SNS** can alert via SMS. Signal: *\"surveillance camera feed → analyze people/objects → text security team\"*. **THE trap:** Kinesis **Data** Streams is for generic streaming records; Kinesis **Video** Streams handles video media. **App2Container** just containerizes existing apps; **SageMaker Autopilot** automates model-building — neither is the managed camera-ingestion pipeline.

## Systems Manager (SSM) suite

One service, several exam-favorite features:

- **Session Manager** — shell access to instances with **no SSH keys, no bastion host, no open port 22**, all logged. Signal: *"secure instance access without opening inbound ports."*
- **Run Command** — execute scripts **across a fleet** at once, no SSH.
- **Patch Manager** — **automated OS patching** on a schedule (maintenance windows).
- **Hybrid**: install the SSM agent on **on-premises servers** and manage them alongside EC2.

## Developer & app services

**AWS Batch** — run **long batch jobs** as Docker containers; schedules onto EC2/Spot for you. Signal: *"job runs for hours"* — the escape hatch from **Lambda's 15-minute limit**.

**AppSync** — managed **GraphQL** API. Signal words: "**GraphQL**," "**real-time subscriptions**," "**offline sync**" for mobile. Any of the three → AppSync.

**Amplify** — full-stack web/mobile app **quickstart**: hosting, auth, backend wired together. Signal: *"frontend team wants to build and deploy quickly without managing infrastructure."*

**SES (Simple Email Service)** — send **application emails** at scale (receipts, notifications, marketing). Signal: *"application must send emails."* (SNS emails are plain-text alerts for ops; SES is for real email.)

**S3 Batch Operations** — run one operation across **billions of EXISTING objects**: copy, tag, restore, or — the exam favorite — **"encrypt all existing objects in the bucket."** Default bucket encryption only covers *new* uploads; Batch Operations fixes history.

**Aurora cloning** (recap) — **copy-on-write clone** of a production Aurora DB in minutes, no full copy, cheap. Signal: *"test against production data quickly without impacting production."*

**X-Ray** — **distributed tracing**: follow one request across microservices to find the slow/broken hop. Signal: *"identify performance bottlenecks across microservices."* (CloudWatch = metrics/logs; X-Ray = the request's journey.)

**AWS Artifact** — self-service portal to download **AWS compliance reports** (SOC, PCI, ISO). Signal: *"auditors need proof of AWS's compliance."* No agents, no config — just the download counter for paperwork.

**Glue ETL ≠ Glue crawler.** A **crawler discovers schema/catalog metadata**; a **Glue ETL job transforms the bytes** (e.g., **2 GB CSV → Parquet**). For *"on every S3 upload"*, send **S3 Object Created → EventBridge → Glue job/workflow**. Lambda can transform small files, but its **15-minute limit** makes large ETL riskier; **Transfer Family/SFTP** is for file-transfer protocols, not S3-to-S3 conversion.

### ACM expiration monitoring

**ACM-managed public certificates normally renew automatically**, but expiring/imported or renewal-blocked certificates still need alerts. **ACM expiration events → EventBridge → SNS**, or **CloudWatch `DaysToExpiry` metric → alarm/SNS**. A scheduled EventBridge workflow can check that metric when the question specifies periodic evaluation.

**THE trap:** *"Notify 30 days before ACM certificate expires"* → **ACM/EventBridge expiration event or `DaysToExpiry` monitoring**, not a custom AWS Config rule by default.

## Question patterns

> *"Existing on-prem app uses RabbitMQ with AMQP; migrate with minimal code changes"* → **Amazon MQ** (protocol keywords = MQ; SQS would mean rewriting)

> *"HTTPS on CloudFront with a custom domain — where's the certificate?"* → **ACM in us-east-1** (CloudFront only reads certs from N. Virginia)

> *"IPv6-only instances need outbound internet access but must block inbound"* → **Egress-only Internet Gateway** (the IPv6 answer; NAT gateway is IPv4)

> *"Automatically extract fields and tables from scanned invoices"* → **Textract** (forms/documents, beyond OCR — not Rekognition)

> *"Migrate a self-managed Apache Kafka cluster with no application changes"* → **MSK** (the word Kafka does all the work)

> *"Mobile app needs a GraphQL API with real-time data sync"* → **AppSync** (GraphQL/subscriptions/offline = AppSync every time)

> *"Automatically apply OS security patches to 500 EC2 instances monthly"* → **SSM Patch Manager** (fleet patching on a schedule)

> *"Encrypt all existing objects already stored in an S3 bucket"* → **S3 Batch Operations** (default encryption only covers new objects)

> *"Data team needs managed Apache Spark for nightly processing at low cost"* → **EMR with Spot task nodes** (Spark = EMR; Spot = cheap)

> *"Regulations require workloads to run in the company's own datacenter using AWS services"* → **Outposts** (AWS racks on your floor — data residency)

> *"Shell access to private instances without SSH keys or bastion hosts"* → **SSM Session Manager** (no port 22, fully audited)

> *"Migrate Oracle to Aurora PostgreSQL"* → **DMS + SCT** (different engines = heterogeneous = add the Schema Conversion Tool)

> *"Convert large CSV files to Parquet after each S3 upload, minimal operations."* → **S3 Object Created → EventBridge → Glue ETL**, not a crawler alone or Lambda + SFTP.

## Pocket card

| Keyword | Answer |
|---|---|
| Alert before ACM certificate expiry | EventBridge ACM events or CloudWatch DaysToExpiry |
| MQTT / AMQP / JMS, minimal rewrite | Amazon MQ |
| Lift-and-shift servers (rehost) | MGN |
| DR replication, RPO seconds | Elastic Disaster Recovery (DRS) |
| DB migration, different engines | DMS + SCT |
| Free auto-renewing TLS certs | ACM |
| CloudFront certificate region | us-east-1 (always) |
| IPv6 outbound-only | Egress-only Internet Gateway |
| On-prem → VPC DNS | Resolver inbound endpoint |
| VPC → on-prem DNS | Resolver outbound endpoint |
| AWS racks in your datacenter | Outposts |
| Single-digit ms to a metro city | Local Zones |
| 5G edge | Wavelength |
| Serverless ETL + catalog | Glue |
| Spark / Hadoop | EMR |
| Kafka | MSK |
| BI dashboards | QuickSight |
| Data lake + fine-grained access | Lake Formation |
| SaaS → S3 | AppFlow |
| Images/video analysis | Rekognition |
| Live camera ingestion → video analysis → SMS | Kinesis Video Streams + Rekognition + SNS |
| Speech → text / text → speech | Transcribe / Polly |
| Sentiment | Comprehend |
| Scanned forms/invoices | Textract |
| Intelligent doc search | Kendra |
| Recommendations / forecasts / chatbots | Personalize / Forecast / Lex |
| Custom ML | SageMaker |
| No-SSH access / fleet scripts / auto-patching | Session Manager / Run Command / Patch Manager |
| Batch jobs > 15 min, Docker | AWS Batch |
| GraphQL, offline sync | AppSync |
| Full-stack quickstart | Amplify |
| Application emails | SES |
| Bulk ops on existing S3 objects | S3 Batch Operations |
| Instant prod DB copy for testing | Aurora cloning |
| Distributed tracing | X-Ray |
| Compliance reports for auditors | AWS Artifact |
| S3 upload → large CSV-to-Parquet conversion | **EventBridge → Glue ETL** (crawler only catalogs) |

That's the last jar on the spice rack — you've now covered the whole SAA-C03 kitchen. Sleep well, read the last sentence first, and go collect your certificate.

## More small-but-tested decision traps

**Neptune Streams** — Neptune is the **graph database**; its native **Streams** feature captures ordered graph data changes. Signal: *"track changes to customer/product relationships in Neptune"* → **Neptune Streams**, not Kinesis added as a second generic stream. [AWS](https://docs.aws.amazon.com/neptune/latest/userguide/streams.html).

**Application Discovery Service + Migration Hub vs MGN** — **Discovery Service inventories** VMs/dependencies, **Migration Hub tracks** the migration portfolio; **MGN replicates/rehosts** machines. THE trap: *"first discover hundreds of VMs and track each migration"* ≠ *"migrate VMs with little downtime."*

**CloudTrail Lake** — SQL queries over AWS API event data. Signal: *"find AccessDenied/Unauthorized AWS API calls across accounts"* → CloudTrail Lake, not QuickSight dashboards or X-Ray tracing. **CloudTrail** audits API calls; **X-Ray** traces an application's request path.

**CloudWatch Container Insights** — container/cluster telemetry for **EKS/ECS** without hand-building all dashboards and agents. **Compute Optimizer** — analyzes utilization to suggest rightsizing; CloudWatch metrics alone do not give the same recommendations.

**Amazon Managed Service for Prometheus ↔ Amazon Managed Grafana** — **Prometheus stores/queries metrics; Grafana visualizes them**. Think *"thermometer readings → chart."* THE trap: Grafana isn't the container-metrics backend feeding Prometheus.

**Lake Formation column-level access** — govern data lake tables/columns with **data filters**; **Athena** queries the S3 lake and **QuickSight** visualizes the resulting datasets. THE trap: an IAM policy on QuickSight users alone is not Lake Formation column governance.

**AWS Transfer Family SFTP + EFS** — managed SFTP backed by EFS, with an **internet-facing VPC-hosted endpoint + security-group IP allowlist** when only trusted public IPs may connect. The service's generic public endpoint does not supply the equivalent VPC security-group control.

**Customer-managed VPC prefix lists + AWS RAM** — maintain reusable CIDRs once and share them across accounts; reference the list in each security group. THE trap: don't invent a Lambda-driven security-group update service if sharing the list directly meets the requirement.

**AWS Backup Vault Lock** — retention immutability (compliance mode for compliance-grade retention). Separate this from **FSx for Windows Multi-AZ** availability and **cross-Region backup copying**.

**EMR cluster node cost rule** — **primary + core On-Demand**, **task Spot** for a transient, data-critical batch cluster. Core nodes hold HDFS data; task nodes are disposable compute. **THE trap:** "cheapest" still has to satisfy the no-data-loss requirement. [AWS node roles](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-master-core-task-nodes.html).

### Question patterns

> *"Graph recommendations database must capture changes with least overhead"* → **Neptune + Neptune Streams**, not Kinesis Data Streams as a replacement for native CDC.

> *"Inventory hundreds of on-prem VMs; centrally track migration"* → **Application Discovery Service + Migration Hub**; MGN is the move itself.

> *"CloudTrail API events must be searched for IAM authorization errors"* → **CloudTrail Lake**, not application X-Ray traces.

> *"Container metrics stored and queried, then displayed as dashboards"* → **Managed Prometheus → Managed Grafana**, in that direction.

> *"NLB targets pass TCP yet return bad HTTP responses"* → **NLB HTTP health check**, not necessarily a replacement ALB.

> *"10-hour EMR batch, save money, protect HDFS data"* → **On-Demand primary/core + Spot task nodes**.

### Pocket card — quick keyword match

| Signal | Pick |
|---|---|
| Graph database mutations | Neptune Streams |
| Discover on-prem VM inventory | Application Discovery Service |
| Track portfolio of migrations | Migration Hub |
| Actually rehost servers | Application Migration Service |
| Analyze API authorization errors | CloudTrail Lake |
| EKS cluster metrics | Container Insights |
| Compute rightsizing recommendations | Compute Optimizer |
| Metrics backend / dashboard | Managed Prometheus / Managed Grafana |
| Data-lake column filtering | Lake Formation data filter |
| Reusable CIDRs across accounts | Customer-managed prefix list + RAM |
| Immutable backup retention | AWS Backup Vault Lock |
| Cheap EMR compute nodes, HDFS retained | Spot task + On-Demand core |
