# AWS VPC

## 📑 Table of Contents

- [VPC Overview](#vpc-overview)
  - [Default VPC](#default-vpc)
- [Subnet Overview](#subnet-overview)
- [IGW & Route Tables](#igw--route-tables)
- [Bastion Hosts](#bastion-hosts)
- [NAT Instance](#nat-instance)
- [NAT GW](#nat-gw)
- [SG & NACL's](#sg--nacls)
  - [Request Example](#request-example)
  - [SG vs NACL](#sg-vs-nacl)
- [VPC Peering](#vpc-peering)
- [VPC Endpoints](#vpc-endpoints)
  - [Private Link Types](#private-link-types)
    - [Interface Endpoints](#interface-endpoints)
    - [Gateway Endpoints](#gateway-endpoints)
- [VPC Flow logs](#vpc-flow-logs)
  - [Levels](#levels)
  - [Output](#output)
  - [Syntax](#syntax)
    - [Examples](#examples)
- [Site to Site VPN, VPG & Customer GW](#site-to-site-vpn-vpg--customer-gw)
  - [CGW behind NAT](#cgw-behind-nat)
  - [AWS VPN CloudHub](#aws-vpn-cloudhub)

## VPC Overview

### Default VPC

- All EC2 have internet and public IPv4 by default
- Public and Private IPv4 DNS records.
- Max CIDR 5 per VPC
- Only private ranges allowed

## Subnet Overview

- Public subnet D
  - Direct internet access via IGW
  - Real IP option
  - Default routing to IGW
  - ALB NAT Bastion hosts
- Private
  - Needs NAT to access internet
  - No real IP
  - Default route to NAT
  - app db workers
- Reserved address first 4 and last (ID GW DNS Extra .. BCast)

Split routing responsibilities by subnet type (public/private) to keep the network clean, secure, and understandable

## IGW & Route Tables

- HA and horizontal scales
- VPC one to one IGW

## Bastion Hosts

## NAT Instance

## NAT GW

- payed per hour and traffic
- single AZ with ENI
- In Public Subnet
- Private Subnet => NATGW => IGW (required)
- 5-100Gbps auto scaling bw
- SG not required

## SG & NACL's

request -> NACL (inbound rules) -> SG (inbound rules) -> EC2

- NACL Stateless
- SG Stateful (if one direction is allowed, the other is auto allowed to)
- New NACL by default DENY *
- NACL subnet level
- SG instance level
- NACL 1-32766
- Default NACL ALLOW IN & OUT by default * (do not modify)
- 32766-60999 Ephemeral ports on Linux (Src Port on client)

### Request Example

Client -> [ Src IP | Src Port | Dst IP | Dst Port | Payload ] -> Dest Server

### SG vs NACL

**SG:** instance level, only allow rules, stateful, all rules evaluation, apply on specific resource
**NACL:** subnet level, allow/deny, stateless, exit on allow, auto apply on all resources

## VPC Peering

- Not transitive
- CIDR not overlapping
- Cross accounts and regions supported
- Can reference SG from peered VPC, but only in the same region

## VPC Endpoints

**Usage:** Within VPC to connect to managed services

SNS Public Link

- Private subnet EC2 -> NAT -> IGW -> SNS (costs)
- Public subnet EC2 -> IGW -> SNS (publicly exposed)

SNS Private Link

- EC2 -> VPC Endpoint -> SNS
- Using private network access
- Redundant and horizontal auto scaling
- DNS and routing check in case of issues

### Private Link Types

#### Interface Endpoints

- Provisions ENI
- Must attach to SG
- Most AWS services
- $ per hour and per GB
- preferred when from on premise access or from different VPC

#### Gateway Endpoints

- Provisions GW
- Used as target in route table, does not need SG
- supports S3 and DDB
- Free

## VPC Flow logs

Interface traffic monitoring & conn troubleshooting

### Levels

- VPC
- Subnet
- ENI

### Output

- S3 (Athena analysis queries. Example: get top REJECTS etc.)
- CW logs (Real time analysis)
- KDF

ELB RDS ECache Redshift Workspaces NatGW TransitGW

### Syntax

version | acc | iface-id | src addr | dst addr | src port | dst port | prot | packets | bytes | start | end | action | log status

#### Examples

- VPC Flow Logs -> CW logs -> CW contributor insights -> top 10 ip addresses
- VPC Flow Logs -> CW logs -- metric filter (ssh,rdp) --> CW alarm -> SNS
- VPC Flow Logs -> S3 -> Athena SQL -> QuickSight

## Site to Site VPN, VPG & Customer GW

Virtual Private Gateway (VGW) <-(S2S VPN Conn)-> Customer VPN GW (CGW)

### CGW behind NAT

- NB! VPC route propagation associated to my subnet route table for
- NB! NAT traversal for CGW

### AWS VPN CloudHub

A low-cost hub-and-spoke model refers to a network architecture where a central hub connects multiple spokes (satellite nodes), but with an emphasis on minimizing costs.

Multi customer VPN's connections to the same VGW which is setup with dynamic routing and config route tables.
