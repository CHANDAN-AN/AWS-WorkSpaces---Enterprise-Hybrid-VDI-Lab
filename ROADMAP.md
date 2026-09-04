# AWS WorkSpaces – Enterprise Hybrid VDI Lab Roadmap

## Purpose

This roadmap is the authoritative implementation sequence for this repository.

**Rule:** do not jump ahead. Complete a volume, validate it, document evidence, then continue.

The existing `Enterprise-Infrastructure-LAB` repository is separate and must not be modified as part of this project.

---

## Status legend

- ⬜ Not started
- 🟡 In progress
- 🟢 Complete / validated
- 🔵 Planned enhancement
- 🔴 Blocked

---

# Phase 0 – Project Preparation

## V00 – Project Initialization — 🟢

- [x] Create dedicated AWS WorkSpaces repository
- [x] Create README
- [x] Create roadmap
- [x] Establish separation from `Enterprise-Infrastructure-LAB`
- [x] Establish documentation and screenshot policy

---

# Phase 1 – AWS Foundation

## V01 – AWS Account Preparation — 🟢

- [x] Confirm AWS account
- [x] Select `ca-central-1`
- [x] Confirm billing awareness
- [x] Confirm WorkSpaces cost controls
- [x] Avoid unnecessary AlwaysOn resources

## V02 – IAM Foundation — 🟢

- [x] Create/use dedicated administrative IAM identity
- [x] Confirm `chandan-admin`
- [x] Confirm AdministratorAccess through Administrators group
- [x] Use `aws login`
- [x] Validate STS identity
- [x] Avoid long-lived access keys
- [ ] Add MFA to administrative IAM identity

## V03 – AWS Resource Governance — 🟡

- [ ] Standardize tags
- [ ] Define naming convention
- [ ] Define cost-allocation tags
- [ ] Review unused resources
- [ ] Establish cleanup checklist

---

# Phase 2 – AWS Networking

## V04 – VPC Foundation — 🟢

- [x] Create VPC `10.20.0.0/16`
- [x] Enable DNS support
- [x] Enable DNS hostnames
- [x] Name VPC `AWS-WorkSpaces-Hybrid-VPC`

## V05 – Subnet Architecture — 🟢

- [x] Create `10.20.10.0/24` in `ca-central-1a`
- [x] Create `10.20.20.0/24` in `ca-central-1b`
- [x] Keep both subnets private
- [x] Use separate AZs

## V06 – AWS Routing — 🟢

- [x] Create private route table
- [x] Associate both WorkSpaces subnets
- [x] Add `10.10.20.0/24` → VGW
- [x] Validate route state

## V07 – Security Groups — 🟢

- [x] Review AD Connector security group
- [x] Keep outbound access available for connector operation
- [ ] Tighten rules where practical after functional validation

---

# Phase 3 – Hybrid Network Connectivity

## V08 – On-Premises Network Preparation — 🟢

- [x] Confirm on-prem `10.10.20.0/24`
- [x] Confirm DC01 `10.10.20.20`
- [x] Confirm DC02 `10.10.20.21`
- [x] Confirm Sophos VLAN 20 gateway `10.10.20.1`
- [x] Confirm Hyper-V topology
- [x] Confirm AWS CIDR does not overlap on-prem

## V09 – AWS Site-to-Site VPN — 🟢

- [x] Create Customer Gateway
- [x] Create Virtual Private Gateway
- [x] Attach VGW to VPC
- [x] Create static-routing VPN
- [x] Configure Sophos route-based IPsec
- [x] Create XFRM interface
- [x] Add Sophos route to AWS VPC
- [x] Configure firewall policies
- [x] Bring Tunnel 1 UP
- [ ] Bring Tunnel 2 UP for HA

## V10 – Hybrid Network Validation — 🟢

- [x] Validate Sophos route lookup
- [x] Validate XFRM route selection
- [x] Use temporary AWS EC2 test endpoint
- [x] Capture failed ICMP path
- [x] Identify incorrect Sophos `AWS-WORKSPACES-VPC` object
- [x] Correct object from `/24` to `/16`
- [x] Re-test DC01 → AWS
- [x] Receive 4/4 ICMP replies
- [x] Terminate temporary EC2
- [ ] Clean up any remaining temporary EC2 test security group/key pair

---

# Phase 4 – Active Directory Integration

## V11 – Active Directory Preparation — 🟢

- [x] Confirm domain `CORP.AC-LAB.TOP`
- [x] Confirm DC01/DC02 DNS
- [x] Confirm `svc_adconnector`
- [x] Place service account in `Service Accounts`
- [x] Create `Workstations/AWS Workspaces` OU
- [x] Delegate required computer-object and validated-write permissions
- [x] Do not grant Domain Admin unnecessarily

## V12 – AWS Directory Service AD Connector — 🟢

- [x] Create AD Connector
- [x] Use both AWS subnets
- [x] Configure on-prem DNS servers
- [x] Use `svc_adconnector`
- [x] Resolve initial failed connector
- [x] Delete failed connector
- [x] Recreate connector after network correction
- [x] Confirm `d-9d6749a1aa` is Active
- [x] Record connector IPs

## V13 – Active Directory Authentication Testing — 🟡

- [x] Register AD Connector with WorkSpaces
- [x] Confirm directory status Registered
- [x] Query on-prem AD users through WorkSpaces
- [x] Confirm 29 users are visible
- [ ] Provision first WorkSpace
- [ ] Test user login with on-prem credentials
- [ ] Confirm domain join
- [ ] Confirm GPO application

---

# Phase 5 – Amazon WorkSpaces Deployment

## V14 – WorkSpaces Architecture — 🟢

- [x] Define Personal WorkSpaces architecture
- [x] Define AD Connector integration
- [x] Define VPN dependency
- [x] Define user authentication path
- [x] Document WSP/DCV and WorkSpaces client flow

## V15 – WorkSpaces Directory Configuration — 🟢

- [x] Register `d-9d6749a1aa`
- [x] Select two AZ-separated subnets
- [x] Confirm `CORP.AC-LAB.TOP`
- [x] Confirm Active Directory identity source
- [x] Confirm user discovery

## V16 – First WorkSpace Deployment — 🟡

- [ ] Select one test user
- [ ] Verify bundle compatibility
- [ ] Prefer AutoStop for cost control
- [ ] Verify root/user volume sizes
- [ ] Decide whether encryption should be enabled
- [ ] Select dedicated WorkSpaces OU if exposed by the current workflow
- [ ] Create first WorkSpace
- [ ] Wait for AVAILABLE state
- [ ] Record WorkSpace ID/private IP/registration status
- [ ] Capture provisioning evidence

## V17 – WorkSpaces Client Deployment — ⬜

- [ ] Install official WorkSpaces client
- [ ] Obtain registration code
- [ ] Register client
- [ ] Sign in as test user
- [ ] Confirm desktop launches
- [ ] Capture end-user evidence

---

# Phase 6 – Domain & Enterprise Management

## V18 – WorkSpace Domain Integration — ⬜

- [ ] Confirm WorkSpace computer object
- [ ] Confirm target OU
- [ ] Confirm domain membership
- [ ] Confirm secure channel

## V19 – Group Policy Management — ⬜

- [ ] Confirm WorkSpace receives existing GPOs
- [ ] Validate `gpresult`
- [ ] Validate workstation security baseline
- [ ] Document applied policies

## V20 – User Profiles & Data — ⬜

- [ ] Test profile persistence
- [ ] Test redirected/network data
- [ ] Document storage strategy
- [ ] Document backup considerations

---

# Phase 7 – Security

## V21 – WorkSpaces Security Hardening — ⬜
## V22 – MFA & RADIUS Integration — 🔵
## V23 – WorkSpaces Network Security — ⬜

---

# Phase 8 – Operations & Troubleshooting

## V24 – WorkSpaces Operational Management — ⬜
## V25 – WorkSpaces Troubleshooting — 🟢

Initial troubleshooting case already documented:
- AD Connector DNS failure
- packet capture
- Sophos route lookup
- incorrect `/24` network object
- corrected `/16`
- successful end-to-end VPN test

## V26 – Monitoring & Logging — ⬜

---

# Phase 9 – Cost Management

## V27 – WorkSpaces Cost Management — 🟡

- [x] Use AutoStop for lab
- [x] Start with one desktop
- [ ] Verify current Free Tier eligibility before additional desktops
- [ ] Monitor billing dashboard
- [ ] Create resource cleanup procedure

---

# Phase 10 – Automation

## V28 – AWS CLI Administration — 🟢
## V29 – PowerShell Automation — ⬜
## V30 – Terraform Infrastructure — ⬜

---

# Phase 11 – Advanced WorkSpaces

## V31 – Custom Images — ⬜
## V32 – WorkSpaces Pools — 🔵
## V33 – Enterprise WorkSpaces Architecture — ⬜

---

# Phase 12 – Interview & Knowledge Validation

## V34 – WorkSpaces Interview Preparation — ⬜
## V35 – Scenario-Based Troubleshooting Assessment — ⬜

---

# Phase 13 – Documentation & Portfolio

## V36 – Technical Documentation — 🟡

- [x] README
- [x] roadmap
- [x] architecture
- [x] implementation log
- [x] troubleshooting record
- [x] resource inventory
- [x] screenshot index
- [ ] final validation evidence

## V37 – Architecture Diagrams — 🟡
## V38 – Project README — 🟢

---

# Phase 14 – Final Validation

## V39 – End-to-End Validation — ⬜
## V40 – Security & Cost Final Review — ⬜
## V41 – Resource Cleanup — ⬜
## V42 – Final Project Review — ⬜
## V43 – Portfolio Publication — ⬜

---

## Current checkpoint

**STOP HERE: V16 – First WorkSpace Deployment**

Do not create multiple WorkSpaces yet. First create and validate one desktop end-to-end.

Required success path:

```text
On-prem AD
   ↓
AD Connector
   ↓
WorkSpaces directory
   ↓
Personal WorkSpace
   ↓
Domain join
   ↓
WorkSpaces Client
   ↓
User authentication
   ↓
Windows desktop
   ↓
GPO / network resource validation
```
