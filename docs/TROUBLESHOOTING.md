# Troubleshooting Record

## Case 001 – Initial AD Connector failure

### Symptom

The first AD Connector deployment failed with:

```text
Connectivity issues detected:
DNS unavailable (TCP port 53) for IP 10.10.20.20
DNS unavailable (TCP port 53) for IP 10.10.20.21
```

### Initial hypotheses

1. VPN routing problem
2. Sophos firewall rule problem
3. DNS reachability problem
4. Incorrect AWS subnet/VPC routing
5. AD Connector security group issue

### Investigation

A temporary Amazon Linux EC2 instance was deployed in:

```text
VPC:     10.20.0.0/16
Subnet:  10.20.10.0/24
IP:      10.20.10.221
```

DC01 attempted to reach the temporary AWS endpoint.

Sophos packet capture showed ICMP arriving from:

```text
10.10.20.20 → 10.20.10.221
```

but traffic was not initially being forwarded through the expected XFRM path.

Sophos route lookup showed the AWS destination should use `xfrm1`.

### Root cause

The Sophos network object:

```text
AWS-WORKSPACES-VPC
```

was configured incorrectly as:

```text
10.20.0.0/24
```

The real VPC is:

```text
10.20.0.0/16
```

The `/24` object did not match destinations elsewhere in the VPC.

### Fix

Changed the Sophos object to:

```text
10.20.0.0/16
```

### Validation

DC01 then successfully pinged:

```text
10.20.10.221
```

with:

```text
4 replies
0% loss
```

This proved:

```text
DC01
  ↓
Sophos VLAN20
  ↓
xfrm1
  ↓
AWS Site-to-Site VPN
  ↓
AWS VPC
  ↓
AWS subnet
  ↓
AWS endpoint
```

### Cleanup

The temporary EC2 instance was terminated after validation.

### Lesson

When troubleshooting route-based VPNs, verify the destination network object and route table independently. A tunnel being UP does not prove that application traffic is correctly routed.

---

## Case 002 – Sophos packet capture interpretation

A packet capture showing:

```text
Status: Incoming
Rule ID: 0
```

only proves that a packet arrived at the firewall interface.

It must not automatically be interpreted as a firewall drop.

The absence of an outgoing packet can have several causes:

- route mismatch
- firewall decision
- object mismatch
- policy mismatch
- tunnel/interface selection
- return-path issue

Use route lookup and a corrected packet filter before concluding that Sophos dropped traffic.

---

## Case 003 – AWS WorkSpaces directory initially unavailable

The WorkSpaces provisioning wizard initially showed no available directory.

The AD Connector had not yet been registered with WorkSpaces.

After registering:

```text
d-9d6749a1aa
CORP.AC-LAB.TOP
```

the WorkSpaces wizard displayed the directory and discovered 29 users.

This proves WorkSpaces can query the on-premises AD through the AD Connector.

---

## Troubleshooting methodology

For future failures, use this order:

1. Check AWS resource state.
2. Check subnet and route-table association.
3. Check Sophos route lookup.
4. Check firewall rules and logs.
5. Check packet capture.
6. Check DNS.
7. Check AD ports.
8. Check AD Connector status.
9. Check WorkSpaces directory registration.
10. Check the WorkSpace/domain join.
