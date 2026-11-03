# Section 27: Snow Family

## The idea

Here's a physics problem AWS can't engineer away: **bandwidth**. Try to upload 100 TB over a 100 Mbps connection and the math says roughly **100+ days** — and that's if nobody else touches the network. Amazon's solution is delightfully low-tech: *never underestimate the bandwidth of a truck full of hard drives*. AWS ships you a ruggedized, encrypted storage box; you copy your data onto it locally at LAN speed; you ship it back; AWS loads it into S3.

**Rule of thumb the exam loves:** if transferring over the network would take **more than about a week**, consider **physical transfer**. For **new customers**, AWS no longer offers Snow Family devices to order — **Data Transfer Terminal** is the current AWS physical-transfer answer where available. Snow device scenarios remain worth recognizing as legacy exam material.

Historically, the Snow family meant AWS ships the disks **to you**. The new alternative flips the trip: with **Data Transfer Terminal, YOU bring the disks to AWS's facility**.\n\nThe second superpower: Snow devices carry **compute**. They can run EC2 instances and Lambda functions on-board, which means you can process data at places with no internet at all — ships at sea, mines, military bases, remote research stations. That's "edge computing."

## The current choice: Data Transfer Terminal\n\n**AWS Data Transfer Terminal** is a **real, secure, physical facility** with high-bandwidth AWS connectivity. Reserve a slot in the console, **bring your own portable storage devices** to the site, connect them, and upload to AWS (including S3). It is **not** a software endpoint or an office network upgrade. Think *drive your disks to AWS's upload room*.\n\n| Requirement | Answer |\n|---|---|\n| One-time 80–250 TB import; office internet far too slow; **physical facility is an option** | **Data Transfer Terminal** — bring disks to the terminal |\n| On-prem network needs **ongoing dedicated** high-throughput connectivity | **Direct Connect** — install a connection to your site |\n| Online file migration/sync over **existing** network | **DataSync** — still limited by available bandwidth |\n| AWS ships a box **to your site** and you return it | **Snowball Edge** — historical/existing-customer pattern, **not available to new customers** |\n\n**THE trap:** *Data Transfer Terminal* doesn't sound like a physical shipment, but **you physically travel with the data**. Direct Connect is a persistent network link, **not** the quick one-time physical-import answer. Also check real-world constraints: terminal locations are limited and, at present, AWS requires **Enterprise Support** for access.\n\nAWS says **no Snow Family devices can be newly ordered by new customers**; existing Snowball Edge customers are unaffected. For edge compute, AWS suggests evaluating **Outposts**. Don't blindly apply the old *\">1 week = order Snowball\"* shortcut to a 2026 scenario.\n\nSources: [AWS Snowball availability change](https://docs.aws.amazon.com/snowball/latest/developer-guide/snowball-edge-availability-change.html) · [Data Transfer Terminal FAQ](https://aws.amazon.com/data-transfer-terminal/faqs/) · [Data Transfer Terminal guide](https://docs.aws.amazon.com/datatransferterminal/latest/userguide/what-is-dtt.html).\n\n## The device lineup (legacy / existing customers)

| Device | Capacity | When |
|---|---|---|
| **Snowcone** | 8–14 TB | Tiny, rugged, ships anywhere (even drone-deliverable); small edge sites |
| **Snowball Edge Storage Optimized** | ~80 TB usable | **The workhorse** — migrations of ~50–500 TB (order several) |
| **Snowball Edge Compute Optimized** | Less storage, more CPU + **optional GPU** | Edge processing: run EC2/Lambda where there's no connectivity |
| **Snowmobile** | Up to **100 PB** | An actual shipping-container truck; consider at **>10 PB** |

Extra flavor points:
- **Snowcone** comes with the **DataSync agent preinstalled** — copy data on it, and it can sync back online later, or you ship it.
- Multiple Snowballs can be ordered in parallel for big jobs (e.g., 200 TB ≈ 3 × 80 TB Snowball Edge Storage Optimized).
- **AWS OpsHub** = the **GUI application** for managing Snow devices (no CLI wrestling required).

## The Snow process (legacy / existing customers)

1. **Order** the device in the AWS console.
2. AWS **ships** it to you.
3. You connect it locally and **copy data — encrypted automatically with KMS** (Key Management Service). Keys never live on the device in usable form.
4. **Ship it back** (E Ink label updates itself).
5. AWS **loads the data into S3**, then securely wipes the device.

THE trap: **Snowball cannot import directly into S3 Glacier.** Data always lands in **S3 first**; if you want Glacier, you attach an **S3 lifecycle rule** that transitions the objects afterward. Any answer choice saying "import from Snowball straight to Glacier" is wrong — reliably tested.

**Edge computing angle:** Snowball Edge Compute Optimized (with optional GPU) runs EC2 AMIs and Lambda locally. Scenario smells: "vessel collecting sensor data with intermittent connectivity," "remote facility must process video before transfer." Process on the device, ship or sync results later.

## Question patterns

> *"Migrate 200 TB, the site has a 100 Mbps link, deadline in 3 weeks; can bring drives to a transfer facility"* → **Data Transfer Terminal** (bring disks to the facility; no new Snow orders for new customers)

> *"Decommission an entire datacenter: multiple petabytes (>10 PB) to AWS"* → **Snowmobile** (past ~10 PB, send the truck)

> *"Research ship must run analysis on collected data with no internet connectivity"* → **Snowball Edge Compute Optimized** (compute at the disconnected edge; GPU if ML is mentioned)

> *"Company wants to archive 80 TB directly into S3 Glacier using Snowball"* → **Import to S3, then lifecycle rule to Glacier** (Snowball can't write to Glacier directly — THE trap)

> *"Small remote clinic needs to transfer ~8–10 TB from a space- and power-constrained site"* → **Snowcone** (tiny footprint, tiny capacity)

> *"Transfer 40 TB once; would take 6 weeks over the existing connection; Data Transfer Terminal is available"* → **Data Transfer Terminal** (physical upload avoids the slow office connection)

> *"Manage Snow devices with a graphical interface"* → **AWS OpsHub** (the Snow GUI)

## Pocket card

| Keyword | Answer |
|---|---|
| One-time huge upload; can bring drives to AWS facility | **Data Transfer Terminal** |\n| Snow Family availability for new customers | **No new orders** — prefer DataSync (online), Data Transfer Terminal (physical), partners |\n| Transfer would take > 1 week | Consider physical transfer; don't automatically order Snow |
| 8–14 TB, tiny/rugged/edge | Snowcone |
| 50–500 TB migration via shipped AWS devices (legacy) | Snowball Edge Storage Optimized |
| Process data offline / GPU at edge | Snowball Edge Compute Optimized |
| > 10 PB, up to 100 PB | Snowmobile |
| Straight to Glacier? | No — S3 first + lifecycle rule |
| Encryption on device | KMS, automatic |
| GUI for Snow devices | OpsHub |
| Preinstalled DataSync agent | Snowcone |

Once your data (and everything else) is in AWS, you'll want to build environments the same way twice without clicking — that's CloudFormation's whole reason to exist.
