# Section 38: AWS Migration, Discovery & Transfer — SAA-C03 Deep Dive

> **Exam objective:** Identify whether the question asks you to **discover**, **assess**, **plan**, **track**, **rehost**, **migrate a database**, or **transfer data**. Most wrong answers solve the wrong verb.
>
> **2026 naming warning:** Older Tutorials Dojo questions may use AWS Application Discovery Service (ADS), AWS Migration Hub and AWS Application Migration Service (MGN). ADS and Migration Hub stopped accepting **new customers on November 7, 2025**; AWS recommends **AWS Transform** for new programs. The old Discovery Connector stopped being supported November 17, 2025. AWS Application Migration Service was renamed **AWS Transform MGN** in June 2026. For legacy multiple choice questions, select the intended legacy service; for current designs, account for lifecycle changes. [AWS ADS notice](https://docs.aws.amazon.com/application-discovery/latest/userguide/application-discovery-service-availability-change.html) · [Migration Hub notice](https://docs.aws.amazon.com/migrationhub/latest/ug/migrationhub-availability-change.html) · [MGN FAQ](https://docs.aws.amazon.com/mgn/latest/ug/General-Questions-FAQ.html).

## 1. The seven verbs (memorize this table)

| Requirement / exam signal | Correct service / pattern | Plausible but wrong |
|---|---|---|
| **Inventory** on-prem VMs, servers, utilization, dependencies | **Application Discovery Service** (legacy), **AWS Transform** (new) | DataSync, MGN replication agent |
| **Calculate costs** and on-prem-vs-cloud business case | **Migration Evaluator** | Migration Hub progress dashboard |
| **Recommend migration strategy** (7 Rs, analyze applications) | Migration Hub **Strategy Recommendations** (legacy); AWS Transform today | MGN alone |
| **Track** each application's migration status | **Migration Hub** (legacy); AWS Transform for new projects | QuickSight alone |
| **Rehost servers** (OS/apps) with minimal changes | **MGN / AWS Transform MGN** | DataSync |
| **Move databases**, including CDC | **DMS**; schema conversion when heterogeneous | MGN for database-level conversion |
| **Copy files / objects** quickly over a network | **DataSync** | Application Discovery Service |
| **Keep SFTP/FTPS/FTP interfaces** for clients/partners | **Transfer Family** | DataSync |
| **Move huge amounts offline** over inadequate bandwidth | **Snowball Edge** (check current availability) | DataSync over a slow circuit |
| **Create physical high-speed ingest session** for portable devices | **Data Transfer Terminal** | Migration Hub |
| **Move to AWS-managed SaaS** | Repurchase (one of the 7 Rs) | MGN |
| **Disaster recovery**, continuous replication with rapid recovery | **Elastic Disaster Recovery (DRS)** | Migration Hub |

**Memory hook:** Discover → Evaluate → Plan → Move → Track. Actual steps overlap: tracking may run throughout.

## 2. AWS Application Discovery Service (ADS): what exactly it discovers

- **Purpose:** capture inventory, configuration, utilization and dependency information about on-prem infrastructure so that migration teams can size, group and plan applications.
- **Discovery Agent:** installed on supported Linux/Windows **physical or virtual servers**; collects host configurations, utilization, running processes, and network connections. **Agent based = more granular OS/process/network insight**.
- **Agentless Collector:** installed as an appliance integrating with **VMware vCenter**; inventory/config/utilization of VMware virtual machines without per-guest agent. Its separate network collection capabilities can help map dependencies, but do not assume all agentless modules have identical collection detail to the agent.
- **Migration Hub import:** upload inventory from external tools/CSV templates rather than running an AWS agent. Import ≠ migration.
- **Agent vs agentless trap:** Physical server + process-level connections → Discovery Agent. Large VMware estate + avoid installing on each guest → Agentless Collector.
- **Critical historic trap:** Discovery **Connector** (older agentless tool) is **not** the same as **Agentless Collector**. The Connector ended November 17, 2025; Tutorial Dojo's connector answer is a legacy exam formulation.
- **Current lifecycle:** ADS closed to new customers November 7, 2025. New projects should consider AWS Transform.

[TD ADS cheat sheet](https://tutorialsdojo.com/aws-application-discovery-service/) · [AWS ADS docs](https://docs.aws.amazon.com/application-discovery/latest/userguide/what-is-appdiscovery.html) · [AWS shutdown list](https://docs.aws.amazon.com/general/latest/gr/full_shutdown_services.html)

## 3. Migration Hub: the application-migration control plane

- **Purpose:** centrally see *which applications/servers are migrating and how far along they are*. Integrates migration status from supported tools such as **MGN** and **DMS**. Discovered servers can be **grouped into applications**.
- **Home Region:** Migration Hub's status/discovery tracking data lives in a designated home Region; migrations themselves can target other supported Regions. Do **not** confuse home Region with the only migration destination.
- **Strategy Recommendations:** legacy assessment of inventory, runtimes, source code and databases to guide modernization/migration choices.
- **Orchestrator:** legacy predefined/customizable migration workflows; distinct from simple status tracking.
- **Refactor Spaces:** facilitate incremental decomposition of monoliths with routing/proxy support (strangler-fig modernization pattern), not server discovery.
- **Migration Hub != QuickSight.** QuickSight can graph data; Migration Hub understands app-level migration state and integration with migration tools.
- **2026:** Migration Hub stopped accepting new customers Nov 7, 2025. AWS Transform replaces it for new migration programs.

[TD Migration Hub](https://tutorialsdojo.com/aws-migration-hub/) · [AWS lifecycle notice](https://docs.aws.amazon.com/migrationhub/latest/ug/migrationhub-availability-change.html)

### The missed Tutorials Dojo question — decomposed

*"Hundreds of on-prem VMs: management wants an **inventory** before migration, and wants to **track migration of each application**."*

1. Inventory = **ADS**, with agent/agentless discovery.
2. Application-level progress dashboard = **Migration Hub**.
3. **MGN** would actually replicate and rehost servers, but does not substitute for the requested pre-migration inventory-and-portfolio answer.
4. **DataSync** copies *data*, not VM inventory; **QuickSight** builds dashboards rather than native app migration tracking.
5. **Legacy multiple choice:** ADS **Discovery Connector** + Migration Hub is the intended answer among the options. **Modern correctness caveat:** Connector is discontinued; ADS and Migration Hub are closed to new customers. For a fresh AWS deployment choose AWS Transform.

## 4. AWS Transform and AWS Transform MGN: learn both names

- **AWS Transform:** new umbrella for discovery, assessments, dependency mapping, migration-wave planning, rehosting and modernization. It brings together capabilities formerly associated with ADS and Migration Hub and extends automation/analysis.
- **AWS Transform MGN:** renamed **AWS Application Migration Service** (June 2026). This is a **replication/rehosting engine**, not another synonym for the entire AWS Transform platform.
- **MGN flow:** install replication agent on source (supported physical, virtual, cloud servers) → replicate disks/block changes to low-cost staging in AWS → test launch → cutover to EC2. Minimal application changes, but you still validate connectivity, drivers, licensing, app dependencies, DNS and cutover.
- **What it preserves:** server workloads/OS/applications via lift-and-shift; it is NOT database schema conversion, data warehouse ETL, or a full application modernization service on its own.
- **Migration vs DR:** MGN for one-time/controlled **rehost/cutover**; **AWS Elastic Disaster Recovery (DRS)** for continuous protection and recovery workflows. Both use continuous replication, so focus on the *business requirement*.
- **Exam cue:** "**hundreds of physical/VM servers with minimal changes and little cutover downtime**" = MGN (new name AWS Transform MGN).

[TD AWS Transform MGN](https://tutorialsdojo.com/aws-transform-mgn/) · [AWS MGN FAQ](https://docs.aws.amazon.com/mgn/latest/ug/General-Questions-FAQ.html) · [AWS Transform changelog](https://docs.aws.amazon.com/transform/latest/userguide/change-log.html)

## 5. Migration Evaluator: cost argument, not move/tracking

- Builds a business case / **TCO** comparing current estate with right-sized AWS options; consumes inventory, utilization and cost assumptions.
- Produces optimized cost scenarios and reports for decision makers. **"CFO wants ROI/estimated AWS savings before approving migration"** → Migration Evaluator.
- Different from ADS (raw discovery), Compute Optimizer (rightsize running AWS workloads), Migration Hub (track), MGN (execute).
- Be cautious with fine-grained collector feature comparisons: the question asks *why* it is used.

[TD Migration Evaluator](https://tutorialsdojo.com/migration-evaluator-cheat-sheet/)

## 6. Database Migration Service (DMS) + schema conversion

- **DMS moves table/data records** between supported sources/targets. Full load, **change data capture (CDC)**, or full load + CDC for low-downtime migration.
- **Homogeneous:** MySQL → RDS MySQL (mostly compatible schema) often DMS alone; **heterogeneous:** Oracle → Aurora PostgreSQL requires schema/code conversion, traditionally **AWS SCT**, with **DMS Schema Conversion** also available for supported workflows. Conversion and data movement are different jobs.
- **CDC ≠ automatic schema transformation.** DMS migration replication handles ongoing data changes; incompatible schema objects, stored procedures and application SQL still require assessment/conversion.
- **DMS ≠ MGN:** If the requirement is *migrate the database into managed RDS/Aurora* and keep it synchronized, use DMS; if the database stays installed on an EC2 VM and you rehost the *entire server*, MGN could be appropriate.
- **DMS ≠ DataSync:** DMS for database rows/CDC and supported data targets; DataSync for files/objects and storage systems.
- **Low downtime:** run initial full load, capture changes while app writes to source, validate, then cut over.

[TD DMS](https://tutorialsdojo.com/aws-database-migration-service/)

## 7. DataSync: storage-copy engine, not discovery

- Managed **online** storage transfers, often accelerated and parallelized: on-prem NFS/SMB/HDFS/object storage to supported AWS storage; copies can also occur between supported AWS storage and supported other clouds/object stores.
- Supports scheduled tasks, transfer verification, metadata handling where compatible, incremental copies, and bandwidth controls. An **agent** may be needed for on-prem transfers: this agent is a **file-transfer worker, not an inventory scanner**.
- **DataSync + Direct Connect / internet** is possible; DataSync is the file-copy orchestration service, DX is the underlying private network connectivity.
- **DataSync vs S3 Transfer Acceleration:** DataSync coordinates bulk file/storage migrations, whereas S3 Transfer Acceleration accelerates S3 PUT/GET uploads via edge endpoints.
- **DataSync vs Storage Gateway:** DataSync is for **copying/migration/sync tasks**; Storage Gateway is for **ongoing hybrid access** to AWS-backed storage through file/volume/tape interfaces.
- **DataSync vs S3 Replication:** DataSync supports sources like NFS/SMB; S3 replication is bucket-to-bucket object replication after configuration, not an on-prem NAS scanner.
- **Trap:** *"DataSync agent uploads inventory of all running VMs to S3"* is wrong. It transfers *files*, not virtualization metadata.

[TD DataSync](https://tutorialsdojo.com/aws-datasync/)

## 8. Transfer Family: preserving external transfer protocols

- Managed endpoints for **SFTP, FTPS, FTP**, and **AS2** workflows with supported AWS storage backends (not every protocol/backend combination is interchangeable).
- Typical client: trading partner, legacy batch integration, automated nightly vendor drop-off. Keep the external protocol while moving storage into S3 or EFS.
- Different from **DataSync** (migration/task-driven transfers) and **Transfer Acceleration** (S3 internet upload optimization).
- **Trap:** *"Partner cannot change SFTP scripts"* → Transfer Family; *"Move 500 TB from NFS to S3 and verify"* → DataSync.

[TD Transfer Family](https://tutorialsdojo.com/aws-transfer-family/)

## 9. Offline and large-scale data ingest

- **Snowball Edge:** AWS-provided physical data-transfer/edge-computing device. Historically ideal when transferring huge datasets online would take too long or bandwidth is unreliable; check current regional/ordering availability rather than assuming every legacy Snow device can be ordered.
- **Snowcone**: smaller edge footprint in legacy questions; read requirements carefully and verify lifecycle if recommending for real projects.
- **AWS Data Transfer Terminal:** physically bring approved portable storage to an AWS-operated transfer location; high-speed data ingest into AWS. Unlike Snowball, **you take your storage to the terminal** rather than AWS shipping you a device to your site.
- **Direct Connect:** dedicated network connectivity, not the *copy engine*. **Site-to-Site VPN** uses encrypted tunnels over IP networks; neither alone handles file enumeration/checksums/retries like DataSync.
- **Rule of thumb:** continuous online NAS copy → DataSync; insufficient network for large one-off load → offline device/terminal option; existing SFTP client ecosystem → Transfer Family.

[TD Snowball Edge](https://tutorialsdojo.com/aws-snowball-edge/) · [TD Data Transfer Terminal](https://tutorialsdojo.com/aws-data-transfer-terminal/)

## 10. The 7 Rs: choose the strategy before the AWS tool

| Strategy | Meaning | Example |
|---|---|---|
| **Retire** | Turn off unused workloads | Decommission forgotten app |
| **Retain** | Keep as-is for now | Regulatory/technical blocker |
| **Rehost** | Lift-and-shift, minimal change | VMware/physical server → EC2 via MGN |
| **Relocate** | Move infrastructure at hypervisor/platform layer | VMware estate to VMware-compatible AWS environment |
| **Replatform** | Some optimization, no wholesale rewrite | Self-managed MySQL → RDS MySQL |
| **Repurchase** | Replace with SaaS | Custom CRM → SaaS |
| **Refactor / rearchitect** | Change architecture for cloud-native benefits | Monolith → services/serverless |

**Gotchas:** Replatform ≠ refactor. Relocate ≠ rehost. Repurchase is not a data-transfer method. Retain/retire can be correct answers: migrating *everything* is not always optimal.

[TD 7 Rs](https://tutorialsdojo.com/aws-migration-strategies-the-7-rs/)

## 11. Common multi-service architectures

### A. Inventory and migrate 500 VMs with tracking (legacy exam)
`On-prem VMware → ADS Agentless Collector → Migration Hub inventory/application grouping → MGN replication and cutover → Migration Hub status`.
2026 greenfield variation: use **AWS Transform** for discovery/assessment/waves and **Transform MGN** for rehosting.

### B. Oracle on premises to Aurora PostgreSQL with little downtime
`Assess and convert schema (SCT or DMS Schema Conversion as supported) → provision Aurora target → DMS full load + CDC → validate → application cutover`.

### C. 200 TB of files on an SMB server to S3
`On-prem SMB → DataSync agent/task → S3`; inspect network capacity/DX, schedule, validation, IAM and S3 access. This does NOT migrate SMB server OS.

### D. Legacy partner sends SFTP uploads to your datacenter
`Partner SFTP client → AWS Transfer Family SFTP endpoint → S3/EFS`; clients keep SFTP. This is NOT server lift-and-shift.

### E. Executive asks whether moving 1,000 servers saves money
`Inventory/utilization + cost assumptions → Migration Evaluator report`; if the request additionally says *track all app waves*, that is a separate tracking requirement.

### F. Recovery of physical/VM estate after regional/site disaster
`Protected machines → Elastic Disaster Recovery continuous replication → drill/test and recovery launch`. Not MGN when the primary requirement is ongoing DR rather than one-time cutover.

## 12. Troubleshooting the wording: exam traps

1. **"Agent installed on-prem" is ambiguous.** Discovery Agent → asset/process/network inventory; MGN agent → block-level server replication; DataSync agent → file transfer; DRS agent → recovery replication. Identify *what* the agent collects or sends.
2. **"Single pane of glass" is ambiguous.** Migration Hub for migration status; CloudWatch for operational telemetry; QuickSight for BI; AWS Organizations for account governance.
3. **"Low downtime" is ambiguous.** MGN for entire servers; DMS CDC for database cutover; DataSync incremental transfers for file datasets.
4. **"Inventory servers" ≠ "copy disks"**. A replication agent knows what it is replicating but this does not make MGN the discovery/portfolio tool being asked for.
5. **"Data is stored in S3" doesn't automatically mean DataSync.** S3 is an endpoint; what are you transferring — logs, objects, VM block data, database records, analytics?
6. **"Visualize" doesn't automatically mean QuickSight.** Migration Hub has native migration state dashboards.
7. **"Hybrid" doesn't automatically mean Outposts.** Outposts runs AWS infrastructure at a customer site; DataSync/Storage Gateway/Direct Connect are other hybrid tools.
8. **"Convert database schema" ≠ DMS data replication.** Schema/code conversion and continuous row replication are distinct.
9. **2026 exam nomenclature trap.** Legacy ADS/Hub/Connector questions can have a historically intended answer even though AWS now recommends Transform and Connector has ended support.
10. **Don't overgeneralize "new AWS Transform"**. **AWS Transform** is the planning/modernization platform; **AWS Transform MGN** is specifically the replication/rehost engine.

## 13. Ten mini-drills (answers immediately below)

1. Need inventory of on-prem VMware VMs without per-VM agent, then app-level tracking (legacy options)?
2. Need running process/network connection detail from physical servers?
3. Need rehost 800 heterogeneous physical and virtual servers without rewriting apps?
4. Need TCO/business justification before migration?
5. Need full load + CDC from Oracle to Aurora PostgreSQL?
6. Need nightly bulk NAS-to-S3 sync with checksums?
7. External customers must retain SFTP clients, target S3?
8. Need managed AWS storage accessible on-prem through file/volume/tape gateways over time?
9. Need continuous low-RPO disaster-recovery protection rather than move-and-cutover?
10. AWS customer starting **new** 2026 VM inventory and migration-wave planning?

<details>
<summary>Answers and why</summary>

1. ADS Agentless Collector + Migration Hub (legacy test); new AWS Transform workflow today. Old Discovery **Connector** is discontinued.
2. Application Discovery Agent (legacy), appropriate deeper host-level discovery.
3. AWS Transform MGN (formerly Application Migration Service).
4. Migration Evaluator.
5. Schema conversion + AWS DMS full load and CDC (not MGN).
6. AWS DataSync (not Transfer Family).
7. AWS Transfer Family.
8. AWS Storage Gateway (not DataSync).
9. AWS Elastic Disaster Recovery.
10. AWS Transform (not a new ADS/Migration Hub customer).
</details>

## 14. Pocket card

| Hear this phrase | Think |
|---|---|
| Inventory servers / dependencies | ADS (legacy) / AWS Transform |
| Physical server process details | Discovery Agent |
| VMware inventory without per-VM agents | Agentless Collector (legacy ADS) |
| Old Discovery Connector | **Unsupported since Nov 2025** |
| All migration progress in one home Region | Migration Hub (legacy) |
| New migration waves and assessments | AWS Transform |
| New name for Application Migration Service | AWS Transform MGN |
| Transfer entire OS + installed application | MGN (rehost) |
| Recovery from disaster rather than permanent migration | DRS |
| Cost comparison / justify migration | Migration Evaluator |
| Full load + database CDC | DMS |
| Different DB engines / stored procedures | Schema conversion + DMS |
| NFS/SMB/HDFS bulk online transfer | DataSync |
| Partner SFTP/FTPS/FTP/AS2 integrations | Transfer Family |
| Ongoing hybrid file/volume/tape access | Storage Gateway |
| Ship offline device because network is too slow | Snowball Edge (legacy/availability caveat) |
| Carry portable media to an AWS high-speed location | Data Transfer Terminal |
| Network circuit from site to AWS | Direct Connect |
| Minimal migration with unchanged architecture | Rehost |
| Small managed-service optimization | Replatform |
| Rewrite as cloud-native | Refactor |
| Replace with SaaS | Repurchase |

## References (read in this order)

- [Tutorials Dojo — Migration & Transfer category](https://tutorialsdojo.com/aws-cheat-sheets-migration-transfer-services/)
- [Tutorials Dojo — Application Discovery Service](https://tutorialsdojo.com/aws-application-discovery-service/)
- [Tutorials Dojo — Migration Hub](https://tutorialsdojo.com/aws-migration-hub/)
- [Tutorials Dojo — AWS Transform MGN](https://tutorialsdojo.com/aws-transform-mgn/)
- [Tutorials Dojo — Migration Evaluator](https://tutorialsdojo.com/migration-evaluator-cheat-sheet/)
- [Tutorials Dojo — AWS DataSync](https://tutorialsdojo.com/aws-datasync/)
- [Tutorials Dojo — Database Migration Service](https://tutorialsdojo.com/aws-database-migration-service/)
- [Tutorials Dojo — Transfer Family](https://tutorialsdojo.com/aws-transfer-family/)
- [Tutorials Dojo — 7 Rs](https://tutorialsdojo.com/aws-migration-strategies-the-7-rs/)
- [AWS — ADS 2025 availability notice](https://docs.aws.amazon.com/application-discovery/latest/userguide/application-discovery-service-availability-change.html)
- [AWS — Migration Hub availability notice](https://docs.aws.amazon.com/migrationhub/latest/ug/migrationhub-availability-change.html)
- [AWS — Transform MGN rename](https://docs.aws.amazon.com/mgn/latest/ug/General-Questions-FAQ.html)

**Exam strategy:** answer the **requirement verb** first, then eliminate tools that solve adjacent but different problems. Where a question is clearly from an old practice bank, distinguish **historical intended exam answer** from **today's AWS deployment guidance**.
