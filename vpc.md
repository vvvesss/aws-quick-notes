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

## AWS Direct Connect DX & Direct Connect GW

### AWS Direct Connect DX

Private connection from customer network to VPC

- required VPG on VPC
- access both public and private resources
- large bw - large data sets lower cost
- low delay real time data feeds
- hybrid environments
- [ AWS VPC VPG <-> AWS Direct connect endpoint <-> Customer partner router ] < Private virtual interface (vlan) - > Customer router in Corp datacenter

For more then 1 VPC connection it is used Direct Connect GW

### AWS Direct Connect GW

VPC1-(VPG1) && VPC2(VPG2) <-> DCGW <-> (Private Virtual Interface VLAN) <-> AWS DC <-> On Premise

### Connection types

- dedicated connections: 1Gbps, 10Gbps 100Gbps
  - dedicated physical port
  - AWS request required
- hosted connections: 50Mbps 500Mbps 10Gbps 
  - AWS request required
  - add/remove capacity on demand
- lead times ~ 1 month
- data transit is not encrypted by default
- DC and VPN are good practice

### Resiliency

- Multiple DC connections
- High resiliency 2 DC locations with 1 line each to 2 on premise 
- Max resiliency 2 DC each with 2 lines to 2 on premises

## Direc Connect + Site to Site VPN

Corporate Data center <-> AWS VPC && Site to Site VPN (for backup purpose)

## Transit GW

**Usage:** for reducing complexity with multiple VPC and on premise connections

- transitive peering between VPC's and on premises
- hub-and-spoke star connection
- regional resource, can work cross region (peering TG)
- share cross-account using Resource Access Manager
- access control can be managed by routing table records
- works with DCGW and VPN connections
- supports IP Multicast like non of any other AWS services

### TG and Site-to-Site VPN with ECMP

- ECMP equal cost multi-path routing
- Routing via multiple best path
- Usecase: **Multiple StoS VPN connection to increase bw to AWS**
- TG share direct connection between multiple accounts 
  - customer datacenter <-vlan-> AWS DC Location Endpoint <-transit VIF-> DCGW <-> TG <-> Multiple VPC's

## Traffic Mirroring 

- Inspect network traffic
- Route to appliance
- Capture From src ENI to dst ENI or NLB
- Filter package
- content inspection, monitoring, ddos protection

## Egress only Internet GW IPv6

Allow outbound traffic only to hide instances from internet access. Routing required.

## Costs

### Traffic per GB

- Internet IN -> VPC Instance = Free
- AZa EC2 <-> AZa EC2 = free
- AZa EC2 <-> AZa EC2 = 0.02$ with Pub/Elastic IP || 0.01$ Private (Fast)
- Inter-region = 0.02$

### S3 per GB

- ingress free
- egress 0.09$
- S3 Transfer Acceleration +0.04-0.08$
- S3 to CF free
- CF to Internet 0.085$ (requests pricing 7X cheaper)
- cross region replication 0.02$

#### EC2 -> NatGW -> IGW -> internet -> S3 BAD!

- 0.045/hour NAT GW to S3 from internet
- 0.045 per GB NAT GW to S3 from internet
- Data transfer out cross region 0.09

#### EC2 -> VPC Endpoint -> S3 

- 0.01$ data tranfsfer in/out same region

## Network Protection

- NALC
- VPC SG
- AWS WAF
- Shield
- Firewall manager

### Network Firewall

- Protect entire VPC
- Layer 3-7
- VPC to VPC
- Outbound to internet
- Inbound from internet
- To / from DC & Site-to-site VPN
- Fine grained control
- IP port prot domain regex
- alert on traffic filtering 
- active flow inspection
- all matches send to S3, CW logs, KDF
