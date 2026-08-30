# Homelab Security Program Baseline

A practical implementation of security controls and GRC principles in a home lab environment, aligned with CIS Controls framework.

## 📋 Project Overview

This project documents the design, implementation, and operation of a small-scale security program for a home lab environment. The goal is to demonstrate:
- Understanding of security frameworks (CIS Controls)
- Practical control implementation across infrastructure
- Incident detection and response capabilities
- Risk management and compliance thinking

**Intended audience:** Network/Security practitioners, HomeLab enthusiasts, fellow IT professionals and whoever can benefit from this information.

---

## 🏗️ Homelab Architecture

### Current Environment

| Component | Purpose | Implementation |
|-----------|---------|-----------------|
| L3 Router | Network Routing LAN/WAN | TP Link AX1100
| Proxmox Host | Compute / Hypervisor | Dell OptiPlex 3070 (i5-9500, 32GB RAM) |
| Pi-hole (CT) | DNS / Network Control | LXC container on Proxmox |
| Tailscale (CT) | Remote Access / VPN | Subnet router, encrypted tunnel |
| Raspberry Pi 2B | DNS Redundancy | Primary DNS, independent hardware |
| NAS (Planned) | Storage / Backups | QNAP TS-462A, RAID 1/5 |
| Wazuh (Planned) | SIEM / Detection | VM on Proxmox |
| Windows AD (Planned) | Identity / Access Control | VM on Proxmox, isolated lab bridge |


### Network Layout
- **Management VLAN (planned):** Infrastructure access via Tailscale
- **Production VLAN:** Pi-hole, backups, media services
- **Lab VLAN (vmbr1, isolated):** Attack/defense exercises, vulnerable apps

---

## 🛡️ CIS Controls Baseline

Current implementation status mapped to CIS Controls v8:

### Implemented Controls

**CIS 1: Inventory of Enterprise Assets**
- [x] Asset inventory documented (systems, IPs, roles)
- [x] Git repo serves as single source of truth
- **Evidence:** IPAM table in this repo

**CIS 2: Inventory of Software Assets**
- [ ] OS/application versions tracked per system
- [x] Patch status monitored (Ubuntu updates, Pi OS, QTS)
- **Evidence:** System config documentation

**CIS 3: Address Unauthorized Software**
- [x] Minimal attack surface (Lite OS, no unnecessary packages)
- [ ] Software allowlist documentation (planned)
- **Evidence:** Proxmox hardening checklist

**CIS 5: Access Control Management**
- [x] SSH key-based authentication on Proxmox
- [x] Strong admin passwords in password manager
- [x] Principle of least privilege for service accounts
- [ ] Formal role-based access control (planned with AD)
- **Evidence:** Proxmox hardening log

**CIS 6: Secure Access Management**
- [x] MFA on Tailscale (TOTP/SSO)
- [x] Unique credentials per system
- [ ] Credential rotation policy (planned)
- **Evidence:** Tailscale configuration

**CIS 8: Audit Logging**
- [x] System logging enabled (Proxmox, Pi-hole query logs)
- [x] Centralized log aggregation via Wazuh (deployed 192.168.10.50)
- [x] Real-time detection verified (SSH brute-force test)
- [x] 30-day log retention configured
- **Evidence:** Wazuh deployment guide + screenshots in repo

**CIS 13: Network Infrastructure Management**
- [x] Firewall enabled (TP-Link, hardened)
- [x] Restricted remote access (Tailscale only, no public ports)
- [x] DNS security (Pi-hole ad-blocking + filtering)
- [x] Network segmentation (planned lab VLAN)
- **Evidence:** Router hardening checklist, Pi-hole config

### Gaps & Future Work

| Control | Gap | Planned Implementation | Timeline |
|---------|-----|----------------------|----------|
| CIS 4 | Vulnerable app discovery | Wazuh vulnerability scans | Month 2 |
| CIS 10 | Data recovery testing | Backup restore drills | Month 1-2 |
| CIS 11 | Secure configuration | Configuration baselines per app | Ongoing |
| CIS 13 | IDS/IPS | Wazuh detection rules | Month 1 |

---

## 📊 Risk Assessment

### Assets
- **Critical:** Pi-hole (DNS), Tailscale (remote access), backups
- **High:** Proxmox host (hypervisor), media data
- **Medium:** Lab VMs, experimental services

### Threats
1. **DNS poisoning / compromise** → Impact: household connectivity, mitigation: redundancy + DNSSEC
2. **Unauthorized remote access** → Impact: full system compromise, mitigation: Tailscale + MFA
3. **Data loss (backup failure)** → Impact: unrecoverable data, mitigation: NAS RAID + offsite copy
4. **Malware in lab environment** → Impact: lab-only (by design), mitigation: isolation + detection

### Current Posture
- **Strong:** Network isolation, access control, redundancy on critical services, centralized logging (Wazuh SIEM deployed)
- **Weak:** Vulnerability scanning (planned), automated incident response (planned)
- **Risk Score:** Medium → Low-Medium (non-production environment, detection/response capabilities now in place)

---

## 📋 Plan of Action & Milestones (POA&M)

| ID | Control | Gap | Action | Owner | Target Date | Status |
|----|---------|-----|--------|-------|------------|--------|
| 1 | CIS 8, 13 | No centralized logging | Deploy Wazuh SIEM | Sam | Week 2 | ✅ COMPLETED |
| 2 | CIS 10 | No backup testing | Restore a VM from backup | Sam | Week 2 | Not Started |
| 3 | CIS 5, 6 | Manual AD management | Deploy Windows AD lab | Sam | Week 3-4 | Not Started |
| 4 | CIS 4 | No vuln scanning | Wazuh vulnerability module | Sam | Week 4 | Not Started |
| 5 | CIS 13 | No network segmentation | Build isolated lab VLAN | Sam | Week 1-2 | Not Started |

---


