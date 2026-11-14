# Section 32: Direct Connect & VPN

## The idea

Everything so far has lived *inside* AWS. But real companies have an office, or a whole datacenter, full of servers that need to talk to their VPCs (Virtual Private Clouds — your private networks in AWS). This section is about building that bridge.

You have two ways to connect your building to AWS. A **Site-to-Site VPN** (Virtual Private Network) is like sending an **armored car on public roads**: your data travels over the ordinary public internet, but wrapped in an encrypted IPsec tunnel so nobody can peek inside. It's fast to arrange and cheap, but you share the roads — traffic jams (latency) happen. **Direct Connect (DX)** is like building **your own private toll road** straight from your building to AWS: a dedicated physical fiber line. Consistent, fast, private — but it takes a long time to pave.

## Site-to-Site VPN

- **Encrypted IPsec tunnel over the public internet.**
- **Setup time: HOURS.** Cheap — you pay a small hourly charge plus data transfer.
- Throughput: roughly **~1.25 Gbps per tunnel**; latency varies because the internet is the internet.
- Two components, one on each side:

| Component | Lives where | What it is |
|---|---|---|
| **Virtual Private Gateway (VGW)** | AWS side | The VPN endpoint attached to your VPC |
| **Customer Gateway (CGW)** | Your side | Represents your on-prem router/firewall |

- **Client VPN** is the cousin: it connects **individual laptops** to a VPC — think "remote employees working from home," not "connect the datacenter."

## Direct Connect (DX)

- **Dedicated private fiber** from your location to AWS. Never touches the public internet.
- **Consistent bandwidth and low latency**: 1, 10, or 100 Gbps.
- **Setup time: WEEKS to MONTHS** (someone has to physically run fiber).
- **THE trap: Direct Connect is NOT encrypted by default.** Private is not the same as encrypted. If a question demands encryption over DX, the answer is **run a Site-to-Site VPN over the Direct Connect link** (IPsec on the private road).

Virtual interfaces (VIFs) — how traffic gets sorted on the fiber:

| VIF type | Goes to |
|---|---|
| **Private VIF** | Your VPC |
| **Public VIF** | AWS public services (S3, DynamoDB endpoints) |
| **Transit VIF** | A Transit Gateway |

**Direct Connect Gateway** lets **one DX connection reach many VPCs across many regions** — no need for a separate fiber per VPC.

## Resiliency patterns

- **DX + VPN failover** — THE classic exam pattern. "We need a **cost-effective backup** for our Direct Connect" → add a **Site-to-Site VPN** as standby.
- **Two DX connections** (ideally at different locations) → maximum resiliency, maximum cost.
- **VPN now, DX later** — need connectivity this week while the fiber is being installed? Start with VPN, cut over when DX is ready.

## The decision in one breath

- Need it in **hours**, **cheap**, **encrypted** → **Site-to-Site VPN**
- Need **consistent performance**, **large steady data volumes**, or compliance says **"must not traverse the public internet"** → **Direct Connect**
- Want both reliability worlds → **DX primary + VPN backup**

### Scale VPN bandwidth with ECMP (Q61)

An individual Site-to-Site VPN tunnel has a throughput ceiling. **Transit Gateway (TGW) + Equal-Cost Multi-Path (ECMP)** can **aggregate bandwidth across multiple VPN tunnels**. Enable TGW VPN ECMP, use **dynamic BGP routing** and compatible customer-gateway routing; **static routes do not support ECMP**. More customer gateways without ECMP aren't the same thing as aggregating usable throughput.

> *"Many VPNs, bandwidth bottleneck, want aggregate throughput"* → **TGW + ECMP + BGP + additional VPN tunnels**, not a second customer gateway alone.

Source: [AWS Transit Gateway VPN](https://docs.aws.amazon.com/whitepapers/latest/aws-vpc-connectivity-options/aws-transit-gateway-vpn.html).

## Question patterns

> *"Transferring 5 TB nightly; VPN performance is inconsistent"* → **Direct Connect** (dedicated fiber = consistent throughput for big regular transfers)

> *"Cost-effective backup for an existing Direct Connect link"* → **Site-to-Site VPN failover** ("cost-effective backup" is the VPN's middle name)

> *"Data over Direct Connect must be encrypted in transit"* → **Site-to-Site VPN over the DX connection** (DX is private, not encrypted)

> *"Must connect on-premises to AWS within days"* → **Site-to-Site VPN** (DX takes weeks–months; VPN takes hours)

> *"Remote employees' laptops need secure access to the VPC"* → **AWS Client VPN** (individual devices, not a site)

> *"One Direct Connect must reach VPCs in multiple regions"* → **Direct Connect Gateway** (one fiber, many VPCs/regions)

> *"On-prem must reach AWS with no traffic on the public internet"* → **Direct Connect** (the only option that skips the internet entirely)

## Pocket card

| Keyword | Answer |
|---|---|
| Aggregate multiple Site-to-Site VPN tunnels | Transit Gateway ECMP + dynamic BGP (not static routing) |
| Encrypted tunnel, quick, cheap | Site-to-Site VPN |
| VGW + CGW | Site-to-Site VPN components (AWS + customer side) |
| Consistent bandwidth, dedicated | Direct Connect |
| Setup in hours | VPN |
| Setup in weeks–months | Direct Connect |
| Encrypt Direct Connect | VPN over DX |
| Cost-effective DX backup | Site-to-Site VPN |
| One DX → many VPCs/regions | Direct Connect Gateway |
| Remote workers → VPC | Client VPN |
| Private VIF / Public VIF / Transit VIF | VPC / public services / Transit Gateway |

You now know how ONE office connects to AWS — next up, what happens when dozens of VPCs and offices all need to talk to each other without a wiring nightmare: Transit Gateway.
