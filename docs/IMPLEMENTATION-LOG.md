# Implementation Log

## 2026-09-03 – Project initialization

Created a dedicated repository for the AWS WorkSpaces hybrid VDI lab.

The existing enterprise infrastructure repository remains separate.

## AWS CLI / IAM

Configured the AWS CLI login flow for:

```text
chandan-admin
```

Confirmed:

```text
Account: 868216907523
Region: ca-central-1
```

No long-lived access key was created.

## AWS VPC

Created:

```text
AWS-WorkSpaces-Hybrid-VPC
10.20.0.0/16
```

Two private subnets were created in different AZs:

```text
10.20.10.0/24 → ca-central-1a
10.20.20.0/24 → ca-central-1b
```

A private route table was associated with both.

## Hybrid VPN

Created:

```text
Customer Gateway
Virtual Private Gateway
Site-to-Site VPN
```

Configured Sophos as a route-based VPN endpoint.

Tunnel 1 became UP.

Tunnel 2 remains DOWN and is deferred.

## AD Connector failure and investigation

The first AD Connector failed because AWS reported DNS TCP/53 unavailable to:

```text
10.10.20.20
10.10.20.21
```

A temporary EC2 instance was deployed to isolate network behavior.

The temporary endpoint was:

```text
10.20.10.221
```

Sophos packet capture and route lookup were used.

The investigation found:

```text
AWS-WORKSPACES-VPC = 10.20.0.0/24
```

while the actual VPC was:

```text
10.20.0.0/16
```

The object was corrected.

The next test produced:

```text
4/4 replies
0% loss
```

The temporary EC2 instance was then terminated.

## AD preparation

Confirmed:

```text
CORP.AC-LAB.TOP
DC01 10.10.20.20
DC02 10.10.20.21
```

Created:

```text
Workstations/AWS Workspaces
```

Delegated the required rights to:

```text
svc_adconnector
```

## AD Connector recreation

The failed connector was deleted.

A new connector was created using:

- VPC `10.20.0.0/16`
- Subnet A
- Subnet B
- DNS `10.10.20.20`
- DNS `10.10.20.21`
- service account `svc_adconnector`

The new connector became:

```text
ACTIVE
```

Connector IPs:

```text
10.20.10.103
10.20.20.62
```

## WorkSpaces registration

The AD Connector was registered with WorkSpaces Personal.

The directory became:

```text
Registered
```

The WorkSpaces provisioning wizard then discovered 29 users from the on-premises directory.

This was an important milestone because it demonstrated successful WorkSpaces → AD Connector → on-prem AD directory discovery.

## Current checkpoint

The first WorkSpace is being configured.

The correct approach is to deploy one test desktop first, verify the end-to-end user experience, then expand the environment.

## Evidence still required

- first WorkSpace creation
- WorkSpace AVAILABLE state
- client registration
- user login
- domain membership
- GPO validation
- network-resource validation
