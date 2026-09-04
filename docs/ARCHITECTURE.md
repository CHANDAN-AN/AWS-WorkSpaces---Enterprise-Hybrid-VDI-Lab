# Architecture

## 1. Design goal

Provide an enterprise-style WorkSpaces Personal environment where AWS provides the virtual desktop platform while the existing on-premises Active Directory remains authoritative for identity.

## 2. Trust and identity model

```text
User
 │
 ▼
WorkSpaces Client
 │
 ▼
Amazon WorkSpaces
 │
 ▼
AWS Directory Service – AD Connector
 │
 │ secure hybrid network
 ▼
Sophos FW01
 │
 ▼
CORP.AC-LAB.TOP
 ├── DC01 10.10.20.20
 └── DC02 10.10.20.21
```

AD Connector is the integration point. The on-premises AD remains the identity source.

## 3. Network addressing

| Network | Purpose |
|---|---|
| `10.10.20.0/24` | On-prem infrastructure / AD |
| `10.10.30.0/24` | Server network, including FS01 |
| `10.20.0.0/16` | AWS WorkSpaces VPC |
| `10.20.10.0/24` | AWS AZ-A private subnet |
| `10.20.20.0/24` | AWS AZ-B private subnet |
| `169.254.170.252/30` | VPN Tunnel 1 AWS-side tunnel network |
| `169.254.151.176/30` | VPN Tunnel 2 tunnel network |

## 4. VPN

The VPN uses static routing and a route-based Sophos XFRM interface.

Tunnel 1:

- AWS outside endpoint: `3.99.88.168`
- Customer gateway public address at creation: `99.235.71.163`
- Sophos XFRM customer-side address: `169.254.170.254/30`
- AWS tunnel-side address: `169.254.170.253/30`
- State: UP

Tunnel 2:

- AWS outside endpoint: `16.54.169.147`
- Tunnel state: DOWN
- Deferred as an HA enhancement

## 5. Routing

AWS:

```text
10.10.20.0/24 → Virtual Private Gateway
```

Sophos:

```text
10.20.0.0/16 → xfrm1
```

This route is critical because WorkSpaces/AD Connector traffic must be able to reach the on-premises AD/DNS servers.

## 6. Security flow

Required AD Connector traffic includes DNS, Kerberos, LDAP and related domain services. The on-premises firewall must permit the required traffic from the AWS subnet ranges to the domain controllers.

Current lab rules are intentionally broad enough to prove functionality. Security hardening is a later phase.

## 7. Dedicated WorkSpaces OU

The lab created:

```text
CORP.AC-LAB.TOP
└── Workstations
    └── AWS Workspaces
```

`svc_adconnector` was delegated the rights needed to create/manage computer objects in that OU.

This is preferable to unnecessarily granting Domain Admin privileges.

## 8. Resilience

The AWS VPN provides two tunnels, but only Tunnel 1 is currently active.

Current state:

```text
Tunnel 1  ██████████ UP
Tunnel 2  ░░░░░░░░░░ DOWN
```

This is acceptable for a lab functional milestone but is not a production-grade HA implementation.

## 9. End-user path

```text
Corporate user
    ↓
WorkSpaces Client
    ↓
WorkSpaces registration code
    ↓
WorkSpaces service
    ↓
AD Connector
    ↓
On-prem AD authentication
    ↓
Domain-joined WorkSpace
```
