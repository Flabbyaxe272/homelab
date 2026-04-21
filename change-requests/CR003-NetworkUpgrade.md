# Title: Home Network Segmentation & Infrastructure Cleanup

Date: Monday, 20 Apr 2026

Change Request #: CR003

>[!NOTE] TL;DR
>
> The current setup is a consumer router, and then all the devices connected via WI-FI or Ethernet into the router itself.
>
> I want to clean it up by having separate devices for separate functions, like a dedicated router/firewall device, then a managed switch to manage connections to specific devices, turning the consumer router into an AP for better wireless connections.

---

## Table of Contents

- [Purpose](#purpose)
- [Scope](#scope)
- [Plan](#plan)
- [Risk](#risk)
- [Back-Out](#back-out)

---

### Purpose

The current home network relies on a single consumer router to handle all network functions: routing/firewalling, switching, and wireless access. This single-device setup limits visibility into network traffic, reduces flexibility for future expansion, and creates a single point of failure for all connectivity in the home.

This change introduces proper network segmentation by assigning dedicated hardware to each function, following standard practice for home lab and small office environments.

---

### Scope

The following is in scope:

- Replacement of the consumer router as the primary gateway with a dedicated router/firewall device
- Introduction of a managed switch as the central switching fabric
- Reconfiguration of the existing consumer router as a wireless access point (AP) only, with routing and DHCP disabled
- Re-homing of all wired devices through the managed switch
- Validation that all existing devices (wired and wireless) retain internet access post-change

The following is out of scope:

- Changes to ISP equipment or the incoming WAN connection
- Introduction of VLANs or network segmentation beyond the physical layer (deferred to a future change request)
- Changes to any services or servers running on the network
- Upgrading to 2.5GbE (deferred to a future change request)

---

### Plan

#### Phase 1: Preparation (Pre-maintenance window)

- Document the current network topology: IP addresses, DHCP range, DNS settings, and any port forwards on the consumer router
- Acquire dedicated router/firewall device and managed switch
- Pre-configure the router/firewall offline: WAN interface, LAN interface, DHCP server, DNS settings, and any existing port forwards
- Pre-configure the managed switch offline: management IP, port settings
- Pre-configure the consumer router in AP mode offline: disable DHCP, set a static LAN IP outside the DHCP range, leave SSID/password unchanged so wireless clients reconnect automatically

#### Phase 2: Cutover (Maintenance window: expect 30–60 min of downtime)

1. Notify household of planned downtime
2. Power down the consumer router
3. Connect the new router/firewall to the ISP modem/ONT on its WAN port
4. Connect the managed switch to the router/firewall on a LAN port
5. Connect the consumer router (now AP) to the managed switch
6. Connect all wired devices to the managed switch
7. Power everything on in order: router/firewall → managed switch → AP
8. Confirm router/firewall receives a WAN IP from the ISP
9. Confirm a wired device receives a DHCP lease from the new router/firewall and has internet access
10. Confirm a wireless device connects to the existing SSID and has internet access

#### Phase 3: Validation

- Verify all household devices have connectivity (phones, laptops, smart home devices, etc.)
- Confirm any existing port forwards/static leases are functioning
- Update network documentation to reflect the new topology

---

### Risk

| Risk                                            | Likelihood | Impact | Mitigation                                                                                       |
| ----------------------------------------------- | ---------- | ------ | ------------------------------------------------------------------------------------------------ |
| Extended downtime beyond the maintenance window | Medium     | High   | Pre-configure all devices offline before cutover; have rollback steps ready                      |
| Wireless devices fail to reconnect to AP        | Low        | Medium | Keep SSID and password identical on the repurposed AP                                            |
| Consumer router does not support true AP mode   | Low        | High   | Verify AP mode capability before the maintenance window; have a standalone AP as a backup option |
| Missed port forwards or static leases           | Low        | Medium | Document all current router settings before beginning                                            |
| Stakeholder dissatisfaction during downtime     | Medium     | High   | Communicate the maintenance window in advance and get written approval (see sign-off below)      |

---

### Back-Out

If the cutover cannot be completed successfully within the maintenance window:

1. Power down all new hardware
2. Reconnect the consumer router to the ISP modem/ONT in its original configuration
3. Power on the consumer router and confirm all devices restore connectivity
4. Document what failed and schedule a follow-up attempt after root cause is identified

Backout is expected to take less than 10 minutes as the consumer router configuration will not have been modified.

---

### Approvals

**Requester**:

________________________________

**Household Stakeholder (WAF)**:

________________________________
