# AWS Resource Inventory

> Resource identifiers are documented for the lab. Secrets are intentionally excluded.

| Resource | Identifier / value | State / role |
|---|---|---|
| Region | `ca-central-1` | Active |
| VPC | `vpc-0ef19d742c2a0d347` | `10.20.0.0/16` |
| Subnet A | `subnet-0260d48123f233e4e` | `10.20.10.0/24`, ca-central-1a |
| Subnet B | `subnet-0d637dbe01deebf78` | `10.20.20.0/24`, ca-central-1b |
| Route table | `rtb-01cba1187fe7007ae` | Private WorkSpaces route table |
| VGW | `vgw-0d2289fef90105f01` | Attached / available |
| CGW | `cgw-0aa72ea1065d5c765` | Public IP recorded at creation |
| VPN | `vpn-0a520b75b71fc6e41` | Available |
| AD Connector | `d-9d6749a1aa` | Active |
| AD Connector ENI A | `10.20.10.103` | ca-central-1a |
| AD Connector ENI B | `10.20.20.62` | ca-central-1b |
| AD Connector SG | `sg-06154539d73c78b4e` | Active |
| WorkSpaces directory | `d-9d6749a1aa` | Registered |
| Temporary EC2 | `i-09677b927820a4b5d` | Terminated |

## On-premises inventory

| Resource | Address |
|---|---|
| Domain | `CORP.AC-LAB.TOP` |
| DC01 | `10.10.20.20` |
| DC02 | `10.10.20.21` |
| Sophos VLAN20 | `10.10.20.1` |
| FS01 | `10.10.30.30` |
| Hyper-V host | `HOMESERVER` |

## Sophos

| Item | Value |
|---|---|
| Firewall | `FW01.CORP.AC-LAB.TOP` |
| XFRM interface | `xfrm1` |
| VPN connection | `AWS_WorkSpaces_Tunnel_1` |
| Route | `10.20.0.0/16 → xfrm1` |
| On-prem object | `ONPREM-LAN = 10.10.20.0/24` |
| AWS object | `AWS-WORKSPACES-VPC = 10.20.0.0/16` |

## Important cleanup

The temporary EC2 test instance was terminated. Review and remove any remaining test-only:

- security group
- EC2 key pair
- local `.pem` file

Do this during V41 cleanup.
