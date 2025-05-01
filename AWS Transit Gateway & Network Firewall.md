# AWS Transit Gateway & Network Firewall

## Table of Contents

1. [MFA and CLI Configuration](#mfa-and-cli-configuration)  
2. [AWS Transit Gateway](#aws-transit-gateway)  
   - [Simplified Network Architecture](#simplified-network-architecture)  
   - [Easier Connectivity](#easier-connectivity)  
   - [SD-WAN Integration](#sd-wan-integration)  
   - [Improved Security](#improved-security)  
   - [Global Scale](#global-scale)  
3. [AWS Network Firewall](#aws-network-firewall)  
   - [Key Concepts](#key-concepts)  
   - [API Access](#api-access)

---

## MFA and CLI Configuration

- MFA enabled using Google Authenticator  
- AWS CLI installed and configured  
- Grafana and partition resizing included

---

## AWS Transit Gateway

### Simplified Network Architecture

AWS Transit Gateway acts as a **cloud router** – each connection is made only once.

As your architecture grows across regions, **inter-Region peering** enables seamless connectivity between Transit Gateways using the **AWS global network**.

This topology simplifies your infrastructure significantly by creating a **hub-and-spoke model** where each VPC only connects once to the Transit Gateway.

---

### Easier Connectivity

Transit Gateway enables **easier network management** by centralizing connectivity:

- Simplifies VPC-to-VPC communication  
- Reduces redundant connections  
- Centralizes routing and control  
- All traffic can be monitored from a single **central console**

---

### SD-WAN Integration

AWS Transit Gateway supports integration with **Software-Defined WAN (SD-WAN)** appliances.

- Supports virtual LAN configurations with full flexibility  
- No **bottlenecks** or **single point of failure**  
- Amazon manages the scaling, redundancy, and backup  
- SD-WAN devices can connect directly to the gateway

---

### Improved Security

Transit Gateway enhances security:

- **All internal traffic is encrypted** within AWS's global private network  
- **Traffic is never exposed** to the public internet  
- Inter-Region peering provides **end-to-end encryption**  
- Built-in protection from DDoS and bandwidth saturation  
- No single point of failure

---

### Global Scale

With **inter-Region peering**, Transit Gateways allow you to:

- Share **VPCs**, **DNS**, **Microsoft Active Directory**, **IPS/IDS**, and other resources across regions  
- Rapidly deploy and manage applications globally  
- Reduce complexity in multi-region designs

---

## AWS Network Firewall

AWS Network Firewall provides **stateful, managed network protection** for your VPC.

It filters traffic going to and from:

- Internet Gateway  
- NAT Gateway  
- VPN  
- AWS Direct Connect  

Built on **Suricata**, an open-source Intrusion Prevention System (IPS), it offers:

- **Stateful inspection**  
- **Deep packet inspection**  
- Detection of protocols like HTTPS, **regardless of port**
- Allow/Deny rules for known bad **domains and IPs**

📚 [AWS Network Firewall Documentation](https://docs.aws.amazon.com/network-firewall/latest/developerguide/what-is-aws-network-firewall.html)

---

### Key Concepts

- **VPC** – A virtual private cloud is an isolated network within AWS.  
- **Internet Gateway** – Allows outbound/inbound internet access for VPC resources.  
- **Subnet** – Logical division of a VPC IP space; firewalls are deployed in subnets.  
- **Firewall Subnet** – Dedicated subnet used only for Network Firewall endpoints.  
- **Route Table** – Defines where traffic goes; can be modified to pass traffic through firewalls.  
- **Firewall** – A managed resource that filters VPC traffic.  
- **Firewall Policy** – Defines the filtering rules and behaviors for the firewall.  
- **Rule Group** – A set of rules (stateless/stateful) that define matching criteria and actions.  
- **Stateless Rules** – Match traffic based on a single packet without flow context.  
- **Stateful Rules** – Analyze full connection flows, allowing more advanced detection and filtering.

---

### API Access

To manage AWS Network Firewall programmatically:

- Use the **REST API endpoint**:  
  `https://network-firewall.<region>.amazonaws.com`

- Or use one of the **AWS SDKs** to simplify authentication, retries, and error handling.

---

### Summary

AWS Transit Gateway and Network Firewall together provide a robust solution for **scalable**, **secure**, and **well-organized network architecture** in AWS. You can centralize routing, control traffic flow, filter and inspect traffic at the edge, and deploy global applications with minimal complexity.

---
