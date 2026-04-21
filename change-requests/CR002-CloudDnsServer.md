# CR002 - Cloud DNS Server: A Provisioning Change Request

**Date:** January 16, 2025
**Category:** DNS Infrastructure

---

## Table of Contents

- [Purpose](#purpose)
- [Scope](#scope)
- [Plan](#plan)
- [Risk](#risk)
- [Back-Out](#back-out)

---

## Purpose

The goal of this change is to gain greater control over content filtering and improve reporting visibility for blocked requests. Open-source filter lists offer broader and more configurable coverage than the current third-party managed DNS service.

A secondary motivation is cost reduction. The current subscription runs approximately $48/year ($4/month). Moving to a self-managed solution - either cloud-hosted or self-hosted - could eliminate or significantly reduce this recurring cost.

---

## Scope

This change is primarily a back-end infrastructure change. From a user perspective, behavior remains the same: ads are blocked, content filters are enforced, and a dashboard remains available for reviewing blocked requests by category and time.

There may be some limitations in per-device attribution depending on router configuration. The overall end-user experience should be transparent.

---

## Plan

Two hosting approaches are under consideration.

### Option A - Self-Hosting

Running the DNS server on existing or new dedicated hardware within the local network. This provides full data ownership but depends on home power and internet reliability. A small dedicated device (i.e., a mini-PC or single-board computer) is recommended for stability over sharing resources with other services.

### Option B - Cloud Hosting

Running the DNS server on a cloud provider. The server would run AdGuard Home with HTTPS query support for added security. Devices that travel outside the home network (laptops, phones) would require a CA certificate installed to trust the DNS server.

### Hosting Comparison

| Feature | Cloud Hosting | Self-Hosting |
|---|---|---|
| Cost | Low monthly fee; some providers offer a free first year | Free if using existing hardware; one-time cost for a dedicated device |
| Performance | Slight latency for DNS queries | Fastest on local network; may vary for remote devices |
| Data control | Third-party managed infrastructure; full control over filters | Full control over both data and filters |
| Availability | Global, highly available | Depends on home power and internet uptime |
| Scalability | Easy to scale | Limited by hardware |

### Cloud Provider Cost Comparison (2-year total)

| Provider | Monthly Cost | Free Tier | Public IP Included | 2-Year Total |
|---|---|---|---|---|
| **AWS** *(recommended)* | $3.15 | 12 months | Yes | **$37.80** |
| Azure | $3.12–$3.90 | 12 months | No (+$2.63/mo) | $109.92 |
| Google Cloud | $3.99 | 3 months ($300 credit) | Yes | $83.79 |
| DigitalOcean | $4.00 | No | Yes | $96.00 |
| *Current service (reference)* | *$4.00* | *-* | *-* | *$96.00* |

AWS is the recommended cloud option: lowest 2-year cost, includes a public IP, and has a 12-month free tier. The new DNS server would be validated and confirmed working before canceling the existing subscription.

---

## Risk

Risk is minimal. The transition does not affect end-user experience. The existing service will remain active during rollout and can be restored at any time if the new setup does not perform as expected.

---

## Back-Out

If the new DNS setup fails or does not meet requirements, network configuration can be reverted to point back to the existing service. No data is lost in the process. The back-out is low-effort and fully reversible.

---

## Open Question

What notification behavior is expected when a blocked site is accessed - alerts, logging only, or both? This may affect which platform or configuration is selected.
