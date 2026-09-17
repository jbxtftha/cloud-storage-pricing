# cloud for storage: how to pick the right type for your files, backups and app data, with real pricing compared

Most people who search "cloud for storage" aren't looking for a definition. They have a pile of files, VM disks, database dumps, video archives or nightly backups sitting somewhere, and they want to know two things: what kind of cloud storage should this data live on, and what will it actually cost per month — including the fees that don't show up until the invoice arrives.

This article walks through both questions with concrete numbers. Along the way it uses Sharktech as the working example, because their storage pricing is unusually simple to verify: a flat per-terabyte rate for S3 object storage, published hourly rates for cloud volumes, and a public cloud plan grid that's actually legible. Everything below comes from their current product pages and ordering portal.

## The three types of cloud storage, and when each one fits

Cloud storage isn't one product. It's three, and picking the wrong one is the most common way people overpay.

**File storage** organizes data in the folders-and-files structure everyone already knows. Good for shared team drives and small-scale collaboration. Overkill for anything at serious volume.

**Block storage** chops data into fixed blocks and serves them to a server as if they were a local disk. Databases, virtual machine boot disks and applications that need low latency live here. You pay more per gigabyte, and the speed is the point.

**Object storage** treats every file as a self-contained "object" in a bucket, addressed through an API. It's built for data that's written once and read occasionally: images, videos, database backups, log archives, compliance records. It's the cheapest of the three per terabyte, and because the S3 API is a de facto industry standard, virtually every backup tool, CI/CD pipeline and media pipeline already knows how to talk to it.

A rough rule of thumb:

- Data attached to a running application → block storage (SSD or NVMe volumes)
- Data accessed by people through a folder structure → file storage
- Everything else — backups, archives, media libraries, artifacts — → object storage

The mistake people make is putting large, rarely-read archives on block storage because that's where the data started. Moving a 10 TB backup archive from SSD volumes to S3-compatible storage can cut that line item dramatically. Sharktech's object storage, for instance, is advertised at a flat $4.90/TB — compare that to typical hyperscaler S3 rates around $0.023/GB (roughly $23/TB) and the size of the gap is obvious.

## Where cloud storage bills actually hide

The sticker price per gigabyte is almost never what sinks a budget. Two things do:

**Egress fees.** Big providers charge for data leaving their platform. Upload a few terabytes, then restore or replicate it later, and the transfer fees can rival the storage fees. This is also the soft form of vendor lock-in: the more data you park, the more it costs to walk away.

**Tiered, region-dependent pricing.** Different rates per region, per availability zone, per storage class, plus line items for requests and retrieval. Reading the invoice requires a decoder ring.

Sharktech's approach sidesteps both by flattening the pricing. Their S3 page states the whole model in one line: storage and bandwidth are the only items on the invoice. Storage is $4.90/TB/month, and the entry package includes 1 TB of bandwidth at no extra charge. The ordering portal currently lists S3 packages starting from $6.00/month, so treat $4.90/TB as the advertised flat rate and check the live order page for the exact package price before committing.

On the cloud compute side the bandwidth policy is similarly blunt: incoming traffic is unlimited and free, 5,000 GB of outgoing is included, and anything beyond that costs $0.002/GB. Your first public IPv4 address is free; extras are $1.50/month each. That's the entire policy — no per-region rate tables, no inter-zone surcharges.

If predictable invoices matter to you, 👉 take a look at Sharktech's current storage and cloud plans and check the numbers against your own usage.

## Full plan comparison: storage plans and pricing

Sharktech sells storage two ways: as standalone S3 object storage, and bundled into cloud plans where you get a resource pool (CPU + RAM + SSD/HDD/NVMe volumes) you can slice into as many VMs as the pool allows. Here is everything currently listed on their ordering portal, in one table.

| Plan | Storage included | Bandwidth | Price (USD) | Billing | Get it |
| --- | --- | --- | --- | --- | --- |
| Object Storage (S3) | 1 TB and up, scales to 1 PB | 1 TB included with entry package | $4.90/TB/month flat (portal lists packages from $6.00/mo) | Monthly | [Order S3 storage](https://bit.ly/SharKTech) |
| Public Cloud – Small | 300–2400 GB SSD, up to 4800 GB HDD, up to 1200 GB NVMe | 20 TB and up | From $39.00/month | Monthly, pay-as-you-go overage | [Order Public Cloud Small](https://bit.ly/SharKTech) |
| Public Cloud – Medium | 800–6400 GB SSD, up to 12800 GB HDD, up to 3200 GB NVMe | 20 TB and up | From $79.00/month | Monthly, pay-as-you-go overage | [Order Public Cloud Medium](https://bit.ly/SharKTech) |
| Public Cloud – Large | 1500–12000 GB SSD, up to 24000 GB HDD, up to 6000 GB NVMe | 20 TB and up | From $249.00/month | Monthly, pay-as-you-go overage | [Order Public Cloud Large](https://bit.ly/SharKTech) |
| Public Cloud – Enterprise | 5000 GB SSD and up, no stated ceiling | 20 TB and up | From $499.00/month | Monthly, pay-as-you-go | [Order Public Cloud Enterprise](https://bit.ly/SharKTech) |
| Dedicated Cloud (Tiny / Small / Medium / Large / Huge / Giant / Colossal) | Fixed prepaid resource pool, sized per tier | Included per tier | Custom-calculated per configuration; an entry configuration displayed at $7.95/mo on the site's calculator | Fixed monthly, prepaid | [Configure Dedicated Cloud](https://bit.ly/SharKTech) |

A few notes on reading this table:

- The "From" prices on Public Cloud reflect the base resource commit; the ranges show how far each plan can burst before you're into hourly overage billing. Small, for example, starts at 4 vCPU / 8 GB RAM / 300 GB SSD and scales to 16 vCPU / 32 GB RAM / 2400 GB SSD within the same plan.
- Public Cloud plans (Enterprise and custom builds excepted) carry a maximum resource cap specifically so a runaway process can't produce a runaway bill.
- Dedicated Cloud is the prepaid sibling of Public Cloud — same OpenStack infrastructure, but you're billed a fixed amount for a fixed pool. Sharktech's product guide puts it plainly: if you pay for 8 cores, you get 8 cores, no more, no less. Prices for the seven tiers are configured through their calculator rather than printed as a static list, and at the time of writing the portal showed the entry package as out of stock, so verify availability before planning around it.

👉 If you want to price a specific configuration yourself, the order portal's calculator is the fastest way to do it.

## How pay-as-you-go storage math works

Public Cloud billing follows one formula, which Sharktech documents like this:

$$\text{Total} = (\text{included resources}) \times (\text{monthly fee}) + (\text{extra usage}) \times (\text{hourly rate})$$

The hourly rates, taken from their cloud page:

- CPU: $0.0025 per core-hour
- RAM: $0.0035 per GB-hour
- NVMe storage: $0.00009 per GB-hour
- SSD storage: $0.00006 per GB-hour
- HDD storage: $0.00002 per GB-hour

Their own worked example: a Large plan (as quoted in their guide, 32 cores / 64 GB RAM / 1500 GB SSD at $287.18/month) running six VMs that together consume 48 cores and 96 GB RAM. Overage is 16 cores and 32 GB RAM, so:

$$287.18 + 16 \times 0.0025 \times 24 \times 30 + 32 \times 0.0035 \times 24 \times 30 = 396.62$$

The practical takeaway: bursting works, and the math is legible enough to forecast. But if your workload runs hot 24/7 past the included commit, you're paying the overage every single month — at that point a fixed Dedicated Cloud pool or the next Public Cloud tier up is usually cheaper. If you only burst during deploys or traffic spikes, pay-as-you-go is exactly what you want.

One detail worth knowing: you can mix storage tiers per VM. Nothing forces a database and a log archive onto the same tier of disk.

## NVMe, SSD or HDD: matching disk speed to the job

Sharktech publishes estimated performance per volume, with the caveat that real results vary with technical factors:

| Tier | Estimated throughput | Estimated IOPS | Typical use |
| --- | --- | --- | --- |
| NVMe | 1.2 GB/s | 18,000 | Databases, AI workloads, anything latency-sensitive |
| SSD | 350 MB/s | 6,000 | General-purpose apps, websites, most workloads |
| HDD | 120 MB/s | 3,000 | Bulk archives, backups, cold data |

The pricing hierarchy runs the same direction as the speed hierarchy — NVMe is the most expensive per gigabyte, HDD the least (which is why the HDD maximums in the plan table are 4× the SSD maximums). The cost-efficient setup for most projects is a small NVMe or SSD volume for the system and database, with a large HDD volume or an S3 bucket attached for everything that's stored rather than served.

That's the honest version of "what's the fastest cloud storage": NVMe, and it costs accordingly. Buying NVMe for a backup archive is paying for speed you'll never use.

## What you get beyond the gigabytes

The infrastructure details matter as much as the price, because storage you can't reach is worthless.

Sharktech runs an OpenStack-based, hyper-converged platform: your resources operate across multiple servers and storage nodes simultaneously, so a single hardware failure doesn't take your data offline. Automatic failover is built in. The company guarantees 99.999% uptime and states it's been in the hosting business for 20 years, serving more than 1,000 businesses.

Locations span Los Angeles, Las Vegas, Denver, Chicago and Amsterdam — enough to keep latency reasonable for either a US or European audience. Everything sits behind a 40G/100G internal network with DDoS protection included, and cloud VMs get private networking, virtual routers, floating IPs, load balancers, IPv6 and a free integrated VPN for hybrid setups.

The lock-in story deserves its own paragraph, because it's the part most providers don't offer. Sharktech lets you download your server disk images at any time — for offsite backup, disaster recovery, or simply to take your workloads to another provider. You can also upload your own ISOs and qcow images. The platform is OpenStack with documented RESTful APIs for compute (Nova), storage (Cinder and Swift), networking (Neutron) and identity (Keystone), and official Linux cloud images updated weekly. In practice this means getting your data out is a feature, not a fight.

Support is humans, reachable by phone, 24/7/365 — which their pricing page makes a point of contrasting with the chatbot-first approach of larger providers. Whether that matters to you depends on who gets paged at 2 a.m. when something breaks.

## What reviewers and users say

Third-party signals on Sharktech are directionally positive but not overwhelming in volume, so calibrate accordingly.

Trustpilot shows an average of 3.5 out of 5, but across a small number of reviews — useful as a signal, not a verdict. HostAdvice gave the company a 2026 recognition for uptime, service quality and support based on independent testing and client feedback, and its expert review of the public cloud product describes it as capable and cost-effective for developers, SMBs and technically minded users. On Web Hosting Talk, a long-running hosting forum, reviewer sentiment focuses on the network quality, attack protection and support responsiveness.

The critical themes that recur across platforms are slower deployment times on discounted hardware and occasional billing disputes. None of that is unusual for the industry, but it's fair context: this is a budget-leaning infrastructure provider, and the trade for the low prices is a smaller operation than AWS — which is precisely why the phone gets answered.

## How to choose: matching plans to scenarios

Pulling it all together into plain recommendations:

1. **You need somewhere to put backups, archives, media or build artifacts.** Start with S3 object storage — at a flat $4.90/TB with the S3 API, it's the cheapest correct answer for write-once data, and your existing tools (Jenkins, GitLab, Terraform, restic, whatever speaks S3) will connect without custom work.
2. **You're running a small app or a handful of sites.** Public Cloud Small at $39/month gives you a pool of 4–16 vCPU and 8–32 GB RAM with 300 GB of SSD to start, burstable when needed. For a couple of VMs and a database, that's comfortable headroom.
3. **You run a multi-tier production stack.** Medium ($79) or Large ($249) covers a real web tier plus database plus staging. Use the burst ceiling for deploys, not for steady state.
4. **Your bill needs to be the same number every month.** That's the entire reason Dedicated Cloud exists — same infrastructure, prepaid fixed pool, seven tiers from Tiny to Colossal. Price it through the calculator before deciding, and check stock on the entry configuration.
5. **You're comparing against AWS or Azure on cost.** Sharktech's own claims run from "at least 40% savings" (their cloud FAQ) to "50–80% versus hyperscalers" (their public cloud pricing page). Those are vendor numbers, not independent benchmarks — but the structural reasons behind them are real: flat rates, free ingress, 5 TB of included egress, and no proprietary platform premium. Run your own workload math against their published rates and the comparison writes itself.

## Getting started

The practical path looks like this: decide the storage type first (object, block or file), then size the tier honestly (does this data need NVMe, or is it sitting in an archive?), then pick the billing model that matches your traffic — flat monthly for predictable workloads, pay-as-you-go for spiky ones.

👉 You can browse the full plan grid — S3 storage, Public Cloud, Dedicated Cloud and their other services — on the Sharktech order portal, and there's a cost calculator on the site for dialing in an exact configuration before you commit.

One last thing worth repeating from their own materials: whether you start small or grow big, the storage rate stays flat and there's no long-term contract. That's a reasonable foundation for a storage setup you might still be using in five years — and if you're not, you can download your images and leave, which is exactly how it should work.
