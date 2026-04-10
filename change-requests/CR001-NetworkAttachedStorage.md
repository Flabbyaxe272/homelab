# Change Request #001 - Network Attached Storage

**Date:** September 9, 2024 **Status:** Approved

---

## Summary

Build a custom NAS to centralize family storage, reduce cloud subscription costs, and establish a homelab foundation for hands-on IT experience. Estimated build cost: ~$400.

---

## Table of Contents

- [Need & Solution](#need--solution)
- [Impact Assessment](#impact-assessment)
    - [Benefits](#benefits)
    - [Potential Risks](#potential-risks)
- [Plan](#plan)
    - [Components](#components)
    - [Operating System](#operating-system)
    - [Initial Services](#initial-services)
    - [Alternatives Considered](#alternatives-considered)
    - [Backup Solution](#backup-solution)
- [Risk Analysis](#risk-analysis)
- [Backout Plan](#backout-plan)
- [Q&A](#qa)
- [NAS vs. Cloud Comparison](#nas-vs-cloud-comparison)

---

## Need & Solution

In an increasingly digital world, managing important documents and media is becoming harder. Several problems needed solving:

**Privacy:** Large cloud providers like Microsoft and Google are frequent targets of cyber attacks, often undisclosed for months. Self-hosting reduces our online footprint and makes our data a less attractive target.

**Fragmentation:** Files are spread across Google Photos, OneDrive, local devices, and flash drives - often with multiple redundant copies and no single source of truth. A NAS consolidates everything into one location with a clear backup strategy: original device, NAS, and NAS backup.

**Reliability:** Cloud services introduce multiple external points of failure - provider outages, internet outages, and potential service shutdowns. A local NAS removes those dependencies for everyday access.

**Career development:** Hands-on homelab experience is increasingly expected in IT roles. This NAS would be the foundation of a broader homelab, providing practical experience with hardware, Linux, networking, and containerized services.

**The solution:** A custom-built server running 24/7, serving as both a Network Attached Storage device and a container hypervisor - the first in a planned homelab infrastructure.

---

## Impact Assessment

### Benefits

**Operational:**

- **Subscription reduction:** Eliminates Microsoft OneDrive subscription (~$70/yr) and reduces dependence on Google Photos
- **Centralized storage:** Single source of truth for documents, photos, and media
- **Automated backups:** Phones and computers back up to the NAS automatically
- **Private cloud:** Self-hosted file sharing and collaboration without third-party dependency
- **Local performance:** LAN-speed file access; no internet dependency for local files

**Learning & Growth:**

- **Hands-on hardware experience:** Building from components provides practical skills in assembly, compatibility verification, and troubleshooting
- **Linux proficiency:** TrueNAS Scale is Linux-based, directly applicable to IT career development
- **Containerization:** Platform for running and managing Docker containers
- **Customization:** Full control over services, configurations, and future expansion

**Cost effectiveness:** Upfront hardware cost amortizes over time to a lower cost-per-GB than ongoing cloud subscriptions, with the ability to expand storage incrementally.

### Potential Risks

- **Upfront cost:** ~$400 initial investment vs. continuing monthly subscriptions
- **Workflow changes:** Photo backup and document management workflows will change; some learning curve expected for all household members
- **Phone battery usage:** Sync services may increase battery consumption; adjustable via settings
- **Electricity cost:** Estimated ~$6.05/month additional cost based on ~60W average draw running 24/7
- **Hard drive lifespan:** HDDs have a finite lifespan (~3-5 years for reliability). Drives will need periodic replacement at ~$150-200/cycle

---

## Plan

### Components

| Component    | Selection                             | Notes                                          |
| ------------ | ------------------------------------- | ---------------------------------------------- |
| CPU          | AMD Ryzen 5 PRO 4650G                 | Supports ECC memory - critical for ZFS/NAS use |
| CPU Cooler   | Noctua NH-U12S Redux                  | Required; CPU ships without cooler             |
| Motherboard  | ASRock X570 Pro 4                     | ECC memory support; AM4 socket compatibility   |
| RAM          | Kingston Server Premier 16GB DDR4 ECC | ECC RAM required for ZFS data integrity        |
| Boot Drive   | Crucial P3 500GB NVMe SSD             | Fast boot and app data storage                 |
| Power Supply | Corsair CX550 80 PLUS Bronze          | Reliable, right-sized for the build            |

All components verified for compatibility prior to purchase. Prices confirmed at time of writing.

### Operating System

**TrueNAS Scale** — selected for the following reasons:

- **Linux-based:** Directly applicable to IT skill development
- **ZFS filesystem:** Self-healing file system that detects and corrects silent data corruption at rest
- **ECC RAM support:** Works in conjunction with ECC memory to catch RAM-level errors before they reach storage
- **Container support:** Native Docker and Kubernetes support for running additional services
- **Snapshots:** Read-only point-in-time copies of the full NAS, enabling fast recovery and offsite backup replication
- **Active community:** Well-documented, widely deployed in both home and enterprise environments

**Alternatives evaluated:**

- **Unraid:** Strong community, but license cost and less native ZFS support made it less appealing
- **OpenMediaVault:** Lighter weight, but fewer built-in features for the planned use case
- **TrueNAS Core (FreeBSD):** Considered, but Scale's Linux base and better container support was preferred

### Initial Services

|Service|Purpose|
|---|---|
|Syncthing|Sync folders, photos, and documents across devices to the NAS|
|Immich|Photo and video management|
|Paperless-ngx|Document management and organization|
|Jellyfin|Media library management and streaming|
|NextCloud|Private cloud storage (replacing OneDrive)|

_Note: WireGuard VPN intentionally excluded from this build — to be implemented on a dedicated edge device in a future CR to avoid exposing the NAS directly to the internet._

### Alternatives Considered

#### Option A: Pre-built NAS (Synology / QNAP)

**Pros:**

- Plug-and-play setup
- Manufacturer support and warranty
- Optimized power efficiency
- Proven reliability

**Cons:**

- Poor resource-to-cost ratio: a comparable 4-bay unit with quad-core CPU and 4GB non-ECC RAM costs ~$600 — the custom build doubles the specs for the same price
- Limited upgradeability; many components are soldered or proprietary
- Closed-source software means security vulnerabilities may go unpatched for extended periods
- End-of-life risk: when the unit is discontinued, hardware replacement means buying a full new unit plus new drives

**Decision:** Custom build selected for better specs, upgradeability, learning value, and cost efficiency.

#### Option B: JBOD Enclosure + Desktop

Attaching a multi-drive enclosure (e.g., Sabrent USB station, ~$200) to an existing Windows desktop via USB 3.0, using DrivePool for software RAID.

**Pros:** Low upfront cost, uses existing hardware

**Cons:** Desktop must remain on 24/7, USB is not ideal for continuous NAS workloads, limits desktop usability, no ECC support, less reliable for always-on use

**Decision:** Not selected. Performance and reliability tradeoffs are not acceptable for a primary family storage device.

### Backup Solution

A NAS alone is not a complete backup strategy. Planned backup approach:

**Offsite snapshots:** TrueNAS snapshot replication to a trusted offsite NAS (family member's system), providing a hot backup with fast recovery capability.

**Cold storage (physical):** For photos and critical documents (taxes, important records), a periodic backup to external hard drive kept in cold storage. An 8TB drive provides ample capacity for all critical data.

_Note: M-Disc Blu-Ray was evaluated as a long-term archival medium (claimed 50-100 year lifespan). This remains an option for archival of irreplaceable photos and documents._

---

## Risk Analysis

| Risk                               | Likelihood                                        | Impact     | Mitigation                                                       |
| ---------------------------------- | ------------------------------------------------- | ---------- | ---------------------------------------------------------------- |
| Hardware failure                   | Low                                               | Medium     | ZFS redundancy; documented replacement process                   |
| Software failure                   | Very Low                                          | Medium     | Snapshot backups; documented recovery procedures                 |
| RAM data corruption                | Very Low (1 in ~30 million)                       | Low-Medium | ECC RAM selected specifically to catch and correct memory errors |
| Data loss from drive failure       | Low-Medium (12% annual failure rate after year 3) | High       | ZFS mirror/RAIDZ; drives replaced on 3-5 year cycle              |
| Natural disaster / physical damage | Very Low                                          | High       | Offsite snapshot replication; cold storage backup                |

---

## Backout Plan

If the plan fails after approval and implementation:

- Return as many components as possible (estimated 75% cost recovery after return shipping)
- Non-returnable items will be repurposed within the household or responsibly recycled
- Resume previous cloud storage subscriptions (OneDrive, Google Photos); note: re-subscription costs may apply
- Data migration back to cloud services from local storage

---

## Q&A

**How is a NAS more secure than cloud storage?** Security here refers primarily to availability and attack surface, not encryption. Major cloud providers are high-value targets attacked constantly. By self-hosting, we become a much smaller and less rewarding target. This does require responsible security practices (least privilege, strong authentication, no unnecessary exposure), but the tradeoff is favorable.

**Why build rather than buy a pre-built NAS?** Covered in detail under Alternatives. Short version: better specs, better value, more upgradeability, and significantly more hands-on learning value.

**Why is a NAS more cost-effective than cloud storage?** Cloud storage charges recurring fees that increase with usage and inflation. A NAS is a one-time hardware cost that provides 2-3x more storage than an equivalent cloud subscription, expandable over time. The cost-per-GB on local storage is substantially lower over any multi-year horizon.

**Why this solution over the alternatives?** The alternatives are not bad — some suit different use cases well. This solution was chosen because it best fits the specific goals: data ownership, hands-on learning, long-term cost efficiency, and a platform for future homelab expansion.

---

## NAS vs. Cloud Comparison

|Category|Self-Hosted NAS|Cloud Service|
|---|---|---|
|**Privacy**|Data stays at home; full control over access|Data on third-party servers; limited visibility into handling|
|**Cost**|One-time hardware investment; lower cost/GB over time|Ongoing subscription fees; increases with storage needs|
|**Speed**|LAN-speed local access; no internet dependency|Limited by internet connection speed; outages = no access|
|**Customization**|Run any service; fully configurable|Fixed feature set; limited to provider's offerings|
|**Security**|You own it; requires responsible configuration|Provider handles it; breach risk is theirs, impact is yours|
|**Offline access**|Full local access without internet|No internet = no access|
|**Failure modes**|Hardware failure (mitigated by redundancy)|Provider outage, shutdown, or breach|
