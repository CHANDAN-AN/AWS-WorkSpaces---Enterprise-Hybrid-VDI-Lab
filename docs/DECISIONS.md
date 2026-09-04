# Architecture & Implementation Decisions

## D001 – Separate repository

The AWS WorkSpaces lab is intentionally separate from the existing enterprise infrastructure repository.

**Reason:** prevent AWS-specific experimentation from changing the established on-premises lab.

## D002 – Hyper-V is the on-premises virtualization platform

All on-premises lab VMs run on the physical `HOMESERVER` Hyper-V host.

**Reason:** this is the actual topology and must be reflected in troubleshooting and architecture documentation.

## D003 – Route-based Sophos VPN

The Sophos VPN uses a route-based tunnel interface/XFRM model.

**Reason:** it provides explicit routing between the AWS VPC and on-premises networks and closely reflects enterprise hybrid connectivity patterns.

## D004 – Static routing for the lab VPN

The AWS VPN uses static routing.

**Reason:** sufficient for the lab and simpler to troubleshoot than introducing BGP before the basic architecture is proven.

## D005 – Tunnel 2 deferred

Only Tunnel 1 is currently UP.

**Reason:** the immediate objective is to build a functioning WorkSpaces environment quickly. Tunnel 2 HA will be added later as a production-hardening exercise.

## D006 – Dedicated AD OU

A dedicated `Workstations/AWS Workspaces` OU was created.

**Reason:** isolate cloud WorkSpaces computer objects and make GPO targeting easier.

## D007 – Least privilege for AD Connector service account

`svc_adconnector` receives delegated rights for the WorkSpaces OU rather than Domain Admin.

**Reason:** follow least-privilege principles.

## D008 – One WorkSpace first

The first WorkSpace is intentionally limited to one test user.

**Reason:** validate the entire architecture before incurring additional WorkSpaces cost or multiplying troubleshooting variables.

## D009 – AutoStop

AutoStop is preferred for the lab.

**Reason:** it minimizes idle hourly usage compared with AlwaysOn.

## D010 – Temporary EC2 diagnostic endpoint

A temporary EC2 instance was used to isolate the VPN/network path from AD Connector-specific behavior.

**Reason:** it provided a clean AWS endpoint that could respond to ICMP and prove routing independently.

The instance was terminated after the test.
