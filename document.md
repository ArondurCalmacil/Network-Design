#  CyberLab Enterprise Network Simulation

  

##  Table of Contents

-   [Project Overview and Goals](#project-overview-and-goals)
-   [Background Research](#background-research)
-   [Project Plan and Timeline](#project-plan-and-timeline)
-   [Implementation Challenges and Solutions](#implementation-challenges-and-solutions)
-   [Network Architecture Design](#network-architecture-design)
-   [Achievements and Outcomes](#achievements-and-outcomes)
-   [Future Extensions](#future-extensions)
-   [References and Resource List](#references-and-resource-list)
-   [Appendix (set up guide)](appendix-(set-up-guide))
----------

## 1. Project Overview and Goals

### 1.1 Project Definition

My project focused on designing and simulating a secure, flexible, and modular virtual network infrastructure that served as a foundation for The CyberLab's various systems. The goal was to create a realistic network "backbone" which included key components such as an Internet Gateway, perimeter firewall (pfSense), DMZ, Internal LAN, and management/monitoring zones—that other teams can later adapt for their applications (like voting systems, office simulations, etc.).

### 1.2 Primary Objectives

#### Develop a Flexible Network Model

Design a network that can handle different scenarios and system requirements by incorporating realistic security and connectivity measures. The architecture needed to support multiple use cases so that other Cyberlab students could "build" their projects on my network infrastructure so they would not have to consider best practices (within reason) and be easily replicable.

#### Learn Networking Fundamentals

This project was originally intended for AWS deployment. The project evolved to explore VirtualBox virtualization, VPCs, subnets, routing, and security groups to understand how virtual networking works in both local and cloud environments. 

#### Integrate Key Network Components

Implement network segmentation through DMZ, internal LAN, and management zones while employing pfSense for firewall and routing tasks. The design incorporates high availability features to ensure resilience and continuity of network services.

### 1.3 Educational Motivation

The project allowed me to learn enterprise network architecture in a practical sense, by the implementation of defense-in-depth security principles, network segmentation strategies, and high availability configurations that mirror real-world enterprise environments.

----------
## 2. Background Research

### 2.1 NIST Cybersecurity Framework Research

This project mainly relies on extensive research into NIST cybersecurity standards and best practices. Key areas of investigation includes:

**Network Segmentation and Boundary Protection**: Research into NIST SP 800-41 Rev 1 provided the theoretical foundation for implementing proper network zones and firewall policies. This publication emphasises the importance of creating distinct security zones based on trust levels and functional requirements, which directly influenced the DMZ, Internal LAN, and Management network design.

**Access Control and Information Flow**: NIST SP 800-53 research, particularly focusing on AC-4 Information Flow Enforcement and SC-7 Boundary Protection controls, guided the development of inter-zone communication policies and access control mechanisms.

**Virtualization Security**: NIST SP 800-125 research addressed security considerations specific to virtualized environments, influencing VirtualBox configuration strategies and virtual network isolation approaches.

### 2.2 pfSense and High Availability Research

Initial research focused on pfSense's basic firewall capabilities, but evolved to include comprehensive investigation of high availability features upon feedback received from my initial designs:

**CARP (Common Address Redundancy Protocol)**: Research into pfSense's implementation of CARP revealed its capability to provide automatic IP failover between primary and secondary firewalls, supporting continuity requirements and effectively creating redundancies.

**State Synchronization (pfsync)**: Investigation of pfsync functionality demonstrated how firewall state tables could be synchronized between HA pairs, ensuring existing connections survive failover events.

**Configuration Synchronization (XMLRPC)**: Research into XMLRPC capabilities showed how configuration changes could be automatically replicated across firewall pairs, reducing administrative overhead and configuration drift.

### 2.3 VirtualBox Networking Research

Extensive research into VirtualBox's networking capabilities was necessary to understand virtual network creation and management:

**Network Adapter Types**: Investigation of NAT, Internal Network, and Host-only networking modes to determine optimal configurations for different network zones.

**Interface Binding and Consistency**: Research revealed the importance of deterministic interface assignment using consistent naming conventions (eth0, eth1, eth2) to prevent configuration drift and maintain network topology integrity.

**Performance Considerations**: Research into VirtualBox's virtualization performance characteristics influenced VM resource allocation and network optimization strategies.

### 2.4 AWS Virtual Networking Research

Although the project ultimately deployed on VirtualBox due to time constraints, research was conducted into AWS networking components:

**VPC Architecture**: Investigation of Virtual Private Cloud design patterns and best practices for creating isolated network environments.

**Subnet Design and Routing**: Research into public and private subnet configurations, route tables, and internet gateway deployment strategies.

**Security Groups and Network ACLs**: Study of AWS's layered security model using security groups and network access control lists for implementing defense-in-depth strategies.

----------

## 3. Project Plan and Timeline

### 3.1 12-Week Project Timeline
The original plan focused on AWS deployment but evolved to prioritize learning fundamental networking concepts through VirtualBox implementation. This change allowed for deeper understanding of network segmentation and high availability concepts.

#### Weeks 1-2: Research and Planning Phase

-   **Week 1**: NIST framework research and cybersecurity best practices investigation
-   **Week 2**: AWS networking research and initial architecture planning

#### Weeks 3-4: Initial AWS Exploration

-   **Week 3**: AWS VPC design and EC2 instance planning
-   **Week 4**: Testing/Playing with EC2 instances which ultimately led to decision to use VirtualBox

#### Weeks 5-6: VirtualBox Environment Setup

-   **Week 5**: VirtualBox installation, host-only network creation, and basic VM deployment
-   **Week 6**: pfSense installation and initial interface configuration

#### Weeks 7-8: Basic Network Implementation

-   **Week 7**: Network zone creation (DMZ, Internal LAN, Management) and service VM deployment
-   **Week 8**: Basic firewall rule implementation and inter-zone connectivity testing

#### Weeks 9-10: Feedback and Redesign Phase

-   **Week 9**: Feedback reception and identification of missing redundancy features
-   **Week 10**: CARP and high availability research phase

#### Weeks 11-12: High Availability Implementation

-   **Week 11**: Secondary pfSense deployment and CARP configuration
-   **Week 12**: Final testing, documentation, and validation

----------

## 4. Implementation Challenges and Solutions

### 4.1 Interface Naming and Binding Issues

**Challenge**: Initial implementation used inconsistent network interface naming, violating networking best practices for deterministic interface assignment.

**Problem Details**: Rather than using standard eth0, eth1, eth2 naming conventions, the initial configuration relied on VirtualBox's default interface assignments, which could change between VM reboots. This created potential security vulnerabilities where firewall rules might apply to incorrect interfaces.

**Solution**: Implemented consistent MAC address assignment for each network adapter and established clear interface naming conventions:

-   eth0: WAN interface (NAT)
-   eth1: LAN interface (Internal Network)
-   eth2: DMZ interface
-   eth3: Management interface
-   eth4: Synchronization interface

This approach ensures reliable interface binding and prevents configuration drift across system reboots.

### 4.2 Network Segmentation Configuration Complexity

**Challenge**: Initial attempts at network segmentation resulted in either overly permissive rules  which defeated security purposes or overly restrictive rules which broke legitimate functionality.

**Problem Details**: Balancing security requirements with operational needs proved challenging, particularly in determining appropriate access controls between network zones.

**Solution**: Implemented a systematic approach based on NIST SP 800-41 guidelines:

-   Started with default-deny policies
-   Added specific allow rules based on documented business requirements
-   Implemented logging for all denied connections to identify legitimate traffic patterns
-   Created comprehensive testing matrix to validate rule effectiveness

### 4.3 High Availability Configuration Challenges

**Challenge**: Upon receiving feedback for initial designs, I was told to add redundancies namely a second pfsense router. This required the implementation of CARP and state synchronization.

**Problem Details**: Adding high availability to an existing single-firewall design required architecture changes and introduced complexity in network routing and state management.

**Solution**: Systematic implementation of pfSense HA features:

-   Deployed secondary pfSense instance with identical network interfaces
-   Configured CARP virtual IPs for each network zone
-   Implemented pfsync for state table synchronization
-   Established XMLRPC for configuration synchronization
-   Created dedicated synchronization network to isolate HA traffic

---


##  5. Network Architecture Design/implementation

For set up guide please refer to appendix

###  Network Topology Overview

The network follows a traditional three-tier architecture DMZ, LAN, and internal services (data) with an added management and synchronization tier. which adds high-availability feature  pfSense CARP. 
![Network Diagram](https://raw.githubusercontent.com/ArondurCalmacil/Network-Design/main/network_diagram.PNG)


###  5.1 Network Segmentation Rationale

####  DMZ (Demilitarized Zone) - 192.168.10.0/24

-  **Purpose**: Hosts public-facing services while maintaining isolation from internal networks. e.g websites/webapps

-  **NIST Justification**: SP 800-41 recommends isolating externally accessible services to limit attack surface/vectors

-  **Access Policy**: Limited outbound access (HTTP/HTTPS only), no access to internal networks

  

####  Internal LAN - 192.168.1.0/24

-  **Purpose**: Houses business-critical applications and data e.g databases and internal applications

-  **NIST Justification**: SP 800-53 AC-4 supports information flow controls between network segments

-  **Access Policy**: Full outbound access, selective inbound access from management network

  

####  Management Network - 192.168.30.0/24

-  **Purpose**: Dedicated network for monitoring, logging, and administrative functions e.g wazuh or ELK stack

-  **NIST Justification**: SP 800-53 SI-4 emphasizes the importance of dedicated monitoring infrastructure

-  **Access Policy**: Administrative access to all networks, restricted inbound access

  

####  Synchronization Network - 192.168.100.0/30

-  **Purpose**: Private network for firewall state synchronization and heartbeat traffic 

-  **Best Practice**: Isolates HA traffic from production networks to prevent interference and security exposure

---
## 6. Achievements and Outcomes

### 6.1 Functional Network Architecture

Successfully implemented a comprehensive virtual network environment that tries to demonstrate enterprise-grade security and availability features:

**Network Segmentation**: Created distinct security zones (DMZ, Internal LAN, Management) with appropriate access controls and traffic isolation.

**High Availability**: Implemented redundant firewall configuration using CARP, pfsync, and XMLRPC synchronization, ensuring network continuity during component failures.

**Security Monitoring**: Integrated Wazuh SIEM for centralized logging and security event monitoring across all network zones.

### 6.2 Best Practices Implementation

The final implementation adheres to industry best practices and NIST guidelines:

**Defense in Depth**: Multiple layers of security controls protect against various attack vectors through network segmentation, firewall rules, and monitoring systems.

**Principle of Least Privilege**: Inter-zone communication follows minimal access requirements, with specific rules for legitimate traffic flows.

**Configuration Management**: Consistent interface naming, MAC address assignments, and documented procedures ensure repeatable deployments.

##  7. Future Extensions

 ### 7.1 AWS Migration Requirements

The ultimate goal remains deployment to AWS infrastructure to support CyberLab's broader objectives. This migration requires several key developments:

**VPC Architecture Translation**: Converting the VirtualBox network design to AWS VPC architecture with appropriate subnets, route tables, and internet gateways.

**EC2 Instance Deployment**: Migrating pfSense and service VMs to EC2 instances with proper security group configurations and elastic network interface assignments.

**High Availability Enhancement**: Leveraging AWS availability zones for geographic redundancy and implementing Auto Scaling groups for dynamic resource management.
  
##  8. References and Resource List

###  NIST Publications

1.  **NIST SP 800-41 Rev 1: Guidelines for Firewall and Firewall Policy**

- URL: https://csrc.nist.gov/publications/detail/sp/800-41/rev-1/final

- Relevance: Foundation for network segmentation and firewall policy design

  

2.  **NIST SP 800-53 Rev 5: Security and Privacy Controls for Information Systems**

- URL: https://csrc.nist.gov/publications/detail/sp/800-53/rev-5/final

- Relevance: Access control (AC-4) and boundary protection (SC-7) implementation

  

3.  **NIST SP 800-125: Guide to Security for Full Virtualization Technologies**

- URL: https://csrc.nist.gov/publications/detail/sp/800-125/final

- Relevance: Virtualization security best practices and hypervisor hardening

  

4.  **NIST SP 800-160 Vol 1: Systems Security Engineering**

- URL: https://csrc.nist.gov/publications/detail/sp/800-160/vol-1/final

- Relevance: Systems engineering approach to security architecture

  

5.  **NIST IR 7298 Rev 3: Glossary of Key Information Security Terms**

- URL: https://csrc.nist.gov/publications/detail/nistir/7298/rev-3/final

- Relevance: Standard terminology and definitions used throughout implementation

  

###  pfSense Documentation

  

6.  **pfSense High Availability Documentation**

- URL: https://docs.netgate.com/pfsense/en/latest/highavailability/index.html

- Relevance: Comprehensive guide to CARP, pfsync, and XMLRPC configuration

  

7.  **pfSense Network Address Translation**

- URL: https://docs.netgate.com/pfsense/en/latest/nat/index.html

- Relevance: NAT configuration and troubleshooting for multi-interface setups

  

8.  **pfSense Firewall Rules**

- URL: https://docs.netgate.com/pfsense/en/latest/firewall/index.html

- Relevance: Firewall rule creation, management, and best practices

  

###  VirtualBox Documentation

  

9.  **VirtualBox User Manual - Chapter 6: Virtual Networking**

- URL: https://www.virtualbox.org/manual/ch06.html

- Relevance: Network adapter types, internal networks, and host-only networking

  

10.  **VirtualBox User Manual - Chapter 8: VBoxManage Command Reference**

- URL: https://www.virtualbox.org/manual/ch08.html

- Relevance: Command-line VM management and automation scripts

  

###  Industry Best Practices

  

11.  **SANS Institute: Network Segmentation Strategies**

- URL: https://www.sans.org/white-papers/36057/

- Relevance: Real-world network segmentation implementation strategies

  
  

###  Security Monitoring and SIEM

  

13.  **Wazuh Documentation**

- URL: https://documentation.wazuh.com/current/index.html

- Relevance: SIEM deployment, agent configuration, and rule customization

  
###  Commands and Code
I acknowledge the use of ChatGPT [https://chatgpt.com/] to generate some commands, dummy data, code and test cases/commands which were used in the project.

the following prompts were entered:

give me commands to test each component of this project based upon my documentation.
give me some html code for a nginx page.
help me set up xmlrpc
help me set up flask api 
Configure five network adapters with specific MAC addresses:
help me configure pfsense
what should the synchronisation configuration look like 
help me set up an sqldb and give me some dummy data
help me set up wazuh




  

---
## 9 Appendix (set up guide)

##  1. Hardware Requirements & VM Specifications

###  1.1 Host System Requirements

My system specs

CPU: AMD 7950X system

RAM: 32GB DDR5

Storage: 1TB SSD

(note: I think these allocations are a bit overkill)

####   Recommended VM Allocations
| VM Component        | vCPUs | RAM  | Storage | Network Adapters                    |
|---------------------|-------|------|---------|-------------------------------------|
| pfSense Primary      | 2     | 2GB  | 20GB    | 5 (WAN, LAN, DMZ, MGMT, SYNC)       |
| pfSense Secondary    | 2     | 2GB  | 20GB    | 5 (WAN, LAN, DMZ, MGMT, SYNC)       |
| DMZ Web Server       | 1     | 2GB  | 20GB    | 1 (DMZ)                             |
| Internal App Server  | 2     | 4GB  | 30GB    | 1 (LAN)                             |
| Database Server      | 2     | 4GB  | 40GB    | 1 (LAN)                             |
| Wazuh SIEM           | 4     | 8GB  | 50GB    | 1 (MGMT)                            |

**Total Resource Usage**: 13 vCPUs, 26GB RAM, 200GB Storage

  

###  1.2 Performance Considerations

  

####  CPU Allocation

- 16-core 7950X provides ample compute resources

- Reserve 25% of cores (4 cores) for host OS operations

- Enable VT-x/AMD-V for optimal virtualization performance

  

####  Memory Management

- Allocate 26GB to VMs, leaving 6GB for host operations

- Enable large pages in VirtualBox for better memory performance

- Consider memory ballooning for dynamic allocation if needed

  

####  Storage Optimization

- Use VirtualBox's VDI format with dynamic allocation

- Enable SSD optimizations in VirtualBox

- Consider separate virtual disks for database storage

---
  

##  2. Step-by-Step Network Setup

  

###  Phase 1: VirtualBox Network Configuration

  

####  Creating Virtual Networks

  

1.  **Open VirtualBox Manager**

```bash

# Launch VirtualBox

virtualbox
```
2.  **Create Host-Only Networks**

Navigate to **File** → **Host Network Manager** and create the following networks:

| Network Name | Purpose              | IP Range            | DHCP     |
|--------------|----------------------|----------------------|----------|
| intnet       | Internal LAN         | 192.168.1.0/24       | Disabled |
| dmznet       | DMZ Zone             | 192.168.10.0/24      | Disabled |
| mgmtnet      | Management           | 192.168.30.0/24      | Disabled |
| syncnet      | HA Synchronization   | 192.168.100.0/30     | Disabled |

  

####  Network Interface Binding Best Practices

**Why NIC Binding Matters:**

- Ensures consistent interface assignment across reboots

- Prevents configuration drift in firewall rules

- Maintains network topology integrity

  

**Implementation Method/Tips:**

1.  **Assign Specific MAC Addresses** to each network adapter

2.  **Document MAC-to-Interface Mappings** for troubleshooting

3.  **Use Consistent Naming Convention** across all VMs

  

###  Phase 2: pfSense Primary Firewall Setup

####  VM Creation and Initial Configuration

1.  **Create pfSense Primary VM**
*(OpenAi's ChatGPT, personal communication, 1/06/2025)*

```bash

# Create VM

VBoxManage createvm --name "pfSense-Primary" --ostype FreeBSD_64 --register

# Configure basic settings

VBoxManage modifyvm "pfSense-Primary" --memory 2048 --cpus 2

VBoxManage modifyvm "pfSense-Primary" --boot1 dvd --boot2 disk

# Create and attach storage

VBoxManage createhd --filename "pfSense-Primary.vdi" --size 20480

VBoxManage storagectl "pfSense-Primary" --name "SATA" --add sata

VBoxManage storageattach "pfSense-Primary" --storagectl "SATA" --port 0 --device 0 --type hdd --medium "pfSense-Primary.vdi"

```

  

2.  **Network Adapter Configuration**

Configure five network adapters with specific MAC addresses:
*(OpenAi's ChatGPT, personal communication, 1/06/2025)*
```bash

# WAN Interface (NAT)

VBoxManage modifyvm "pfSense-Primary" --nic1 nat --macaddress1 080027000001

# LAN Interface (Internal Network)

VBoxManage modifyvm "pfSense-Primary" --nic2 intnet --intnet2 intnet --macaddress2 080027000002

# DMZ Interface

VBoxManage modifyvm "pfSense-Primary" --nic3 intnet --intnet3 dmznet --macaddress3 080027000003

# Management Interface

VBoxManage modifyvm "pfSense-Primary" --nic4 intnet --intnet4 mgmtnet --macaddress4 080027000004

# Sync Interface

VBoxManage modifyvm "pfSense-Primary" --nic5 intnet --intnet5 syncnet --macaddress5 080027000005

```

  

####  pfSense Installation Process

  

1.  **Download pfSense **

- Visit: https://www.pfsense.org/download/

- Select: AMD64 (64-bit) Architecture

- Choose: ISO Installer image

  

2.  **Install pfSense**

- Boot from ISO

- Accept all default installation options

- Install to local disk

- Reboot when prompted

  

3.  **Initial Interface Assignment**

Upon first boot, assign interfaces:

```

WAN -> em0 (First network adapter - NAT)

LAN -> em1 (Second network adapter - intnet)

OPT1 -> em2 (Third network adapter - dmznet)

OPT2 -> em3 (Fourth network adapter - mgmtnet)

OPT3 -> em4 (Fifth network adapter - syncnet)

```

  

####  Interface IP Configuration

  

Configure each interface with static IP addresses following best practices:

  

1.  **WAN Interface Configuration**

- Type: DHCP (receives IP from VirtualBox NAT)

- Purpose: Internet connectivity and external access

  

2.  **LAN Interface Configuration**

```

IP Address: 192.168.1.1

Subnet Mask: 255.255.255.0 (/24)

DHCP Server: Disabled (static assignments recommended)

```

  

3.  **DMZ Interface (OPT1) Configuration**

```

IP Address: 192.168.10.1

Subnet Mask: 255.255.255.0 (/24)

Description: DMZ_Network

DHCP Server: Disabled

```

  

4.  **Management Interface (OPT2) Configuration**

```

IP Address: 192.168.30.1

Subnet Mask: 255.255.255.0 (/24)

Description: Management_Network

DHCP Server: Disabled

```

  

5.  **Sync Interface (OPT3) Configuration**

```

IP Address: 192.168.100.1

Subnet Mask: 255.255.255.252 (/30)

Description: HA_Sync_Network

DHCP Server: Disabled

```

  

###  Phase 3: pfSense Secondary Firewall Setup

  

####  VM Creation for Secondary Firewall

  

1.  **Clone Primary Configuration** 

```bash

# Create secondary VM with identical specs

VBoxManage createvm --name "pfSense-Secondary" --ostype FreeBSD_64 --register

VBoxManage modifyvm "pfSense-Secondary" --memory 2048 --cpus 2

# Network adapters with different MAC addresses

VBoxManage modifyvm "pfSense-Secondary" --nic1 nat --macaddress1 080027000011

VBoxManage modifyvm "pfSense-Secondary" --nic2 intnet --intnet2 intnet --macaddress2 080027000012

VBoxManage modifyvm "pfSense-Secondary" --nic3 intnet --intnet3 dmznet --macaddress3 080027000013

VBoxManage modifyvm "pfSense-Secondary" --nic4 intnet --intnet4 mgmtnet --macaddress4 080027000014

VBoxManage modifyvm "pfSense-Secondary" --nic5 intnet --intnet5 syncnet --macaddress5 080027000015

```

  

2.  **Secondary Interface IP Configuration**

```

WAN: DHCP (VirtualBox NAT)

LAN: 192.168.1.2/24

DMZ: 192.168.10.2/24

MGMT: 192.168.30.2/24

SYNC: 192.168.100.2/30

```

  

###  Phase 4: Basic Firewall Rules Configuration

  

Before implementing HA, establish basic connectivity rules:

  

####  WAN Rules (Default)

-  **Inbound**: Block all unsolicited connections

-  **Outbound**: Allow established/related connections

  

####  LAN Rules

```

Protocol: Any

Source: LAN net (192.168.1.0/24)

Destination: Any

Action: Allow

Description: LAN to Any - Default allow rule

```

  

####  DMZ Rules

```

Protocol: TCP

Source: DMZ net (192.168.10.0/24)

Destination: Any

Destination Port: 80, 443

Action: Allow

Description: DMZ HTTP/HTTPS outbound only

```

  

####  Management Rules

```

Protocol: Any

Source: MGMT net (192.168.30.0/24)

Destination: Any

Action: Allow

Description: Management network full access

```

  

```

Protocol: TCP

Source: Any

Destination: MGMT net (192.168.30.0/24)

Destination Port: 22, 443, 9200

Action: Allow

Description: SSH, HTTPS, and Wazuh access to management
```
---

  

##  3. High Availability Implementation

  

###  Understanding pfSense HA Components

  

####  CARP (Common Address Redundancy Protocol)

-  **Function**: Provides automatic IP failover between primary and secondary firewalls

-  **Mechanism**: Uses heartbeat messages to detect failures and promote backup to primary

-  **NIST Alignment**: Supports SP 800-53 CP-10 (System Recovery and Reconstitution) by ensuring rapid service failover and continuity.

  

####  pfsync (pfSense State Synchronization)

-  **Function**: Synchronizes firewall state tables between HA pair

-  **Benefit**: Maintains existing connections during failover

-  **Network Requirement**: Dedicated sync interface to prevent state table exposure

  

####  XMLRPC Configuration Synchronization

-  **Function**: Automatically replicates configuration changes from primary to secondary

-  **Scope**: Firewall rules, NAT rules, user accounts, and most system settings

-  **Limitation**: Some settings (interface IPs, CARP settings) must be configured manually

  

###  CARP Virtual IP Configuration
####  Primary Firewall CARP Setup
1.  **Navigate to Firewall → Virtual IPs → Add**

3.  **LAN CARP VIP Configuration**

```

Type: CARP

Interface: LAN

Address: 192.168.1.254/24

Virtual IP Password: SecureHAPassword123

VHID Group: 1

Advertising Frequency: Base: 1, Skew: 0

Description: LAN_CARP_VIP

```

  

3.  **DMZ CARP VIP Configuration**

```

Type: CARP

Interface: DMZ

Address: 192.168.10.254/24

Virtual IP Password: SecureHAPassword123

VHID Group: 2

Advertising Frequency: Base: 1, Skew: 0

Description: DMZ_CARP_VIP

```

  

4.  **Management CARP VIP Configuration**

```

Type: CARP

Interface: MGMT

Address: 192.168.30.254/24

Virtual IP Password: SecureHAPassword123

VHID Group: 3

Advertising Frequency: Base: 1, Skew: 0

Description: MGMT_CARP_VIP

```

  

####  Secondary Firewall CARP Setup

  

Configure identical CARP VIPs on secondary with higher skew values:

  

```

LAN CARP: 192.168.1.254/24, VHID: 1, Skew: 100

DMZ CARP: 192.168.10.254/24, VHID: 2, Skew: 100

MGMT CARP: 192.168.30.254/24, VHID: 3, Skew: 100

```

  

**Skew Value Explanation**: Higher skew = lower priority. Secondary will only become active if primary fails.

  

###  pfsync Configuration

  

####  Primary Firewall pfsync Setup

  

1.  **Navigate to System → High Avail. Sync**

  

2.  **State Synchronization Settings**

```

Synchronize States: Checked

Interface: SYNC (192.168.100.1)

pfsync Synchronization Peers: 192.168.100.2

Description: Sync firewall states to secondary

```

  

####  Secondary Firewall pfsync Setup

```

Synchronize States: Checked

Interface: SYNC (192.168.100.2)

pfsync Synchronization Peers: 192.168.100.1

```

  

###  XMLRPC Configuration Sync

  

####  Primary Firewall XMLRPC Setup

  

1.  **System → High Avail. Sync → Configuration Synchronization Settings**

  

2.  **Synchronization Configuration**
*(OpenAi's ChatGPT, personal communication, 1/06/2025)*
```

Synchronize Config: Checked

Remote System IP: 192.168.100.2

Remote System Username: admin

Remote System Password: [Secondary admin password]

Synchronization Options (Select All):

☑ Synchronize Users and Groups

☑ Synchronize Certificates

☑ Synchronize Firewall Rules

☑ Synchronize NAT Rules

☑ Synchronize Schedules

☑ Synchronize Aliases

☑ Synchronize Static Routes

☑ Synchronize Load Balancer

☑ Synchronize IPsec

☑ Synchronize OpenVPN

☑ Synchronize DHCP Server

☑ Synchronize Wake on LAN

```

  

###  HA Preemption and Advanced Settings

  

####  Preemption Configuration

-  **Enable Preemption**: Allows primary to reclaim MASTER role after recovery

-  **Location**: System → High Avail. Sync → Settings

-  **Recommendation**: Enable with 30-second delay to prevent flapping

  

####  CARP Maintenance Mode

-  **Purpose**: Temporarily demote primary for maintenance

-  **Command**: `pfctl -d` (disable packet filtering)

-  **Recovery**: `pfctl -e` (enable packet filtering)

  

---

  

##  4. Service Configuration

  

###  DMZ Web Server Setup

  

####  Ubuntu Server VM Creation

1.  **Create DMZ Web Server VM**
```bash

VBoxManage createvm --name "DMZ-WebServer" --ostype Ubuntu_64 --register

VBoxManage modifyvm "DMZ-WebServer" --memory 2048 --cpus 1

VBoxManage modifyvm "DMZ-WebServer" --nic1 intnet --intnet1 dmznet

```

  

2.  **Install Ubuntu Server 22.04 LTS**

- Download Ubuntu Server ISO

- Install with minimal configuration

- Enable SSH during installation

  

####  Static IP Configuration
*(OpenAi's ChatGPT, personal communication, 1/06/2025)*
```bash

# Edit netplan configuration

sudo nano  /etc/netplan/00-installer-config.yaml

  

network:

ethernets:

enp0s3:

addresses:

- 192.168.10.10/24

gateway4: 192.168.10.254  # CARP VIP

nameservers:

addresses: [8.8.8.8, 8.8.4.4]

version: 2

  

# Apply configuration

sudo netplan  apply

```

  

####  NGINX Installation and Configuration
*(OpenAi's ChatGPT, personal communication, 1/06/2025)*
```bash

# Update system

sudo apt  update  && sudo apt  upgrade  -y

  

# Install NGINX

sudo apt  install  nginx  -y

  

# Create custom index page

sudo tee  /var/www/html/index.html  >  /dev/null  <<EOF

<!DOCTYPE html>

<html>

<head>

<title>CyberLab DMZ Web Server</title>

<style>

body { font-family: Arial, sans-serif; margin: 40px; }

.header { color: #2c3e50; border-bottom: 2px solid #3498db; }

.info { background-color: #ecf0f1; padding: 15px; margin: 20px 0; }

</style>

</head>

<body>

<h1 class="header">CyberLab Enterprise Network Simulation</h1>

<div class="info">

<h2>DMZ Web Server</h2>

<p><strong>Server IP:</strong> 192.168.10.10</p>

<p><strong>Network Zone:</strong> Demilitarized Zone (DMZ)</p>

<p><strong>Purpose:</strong> Public-facing web services</p>

<p><strong>Security Level:</strong> Medium - Internet accessible</p>

</div>

<h3>Network Information</h3>

<ul>

<li>Gateway: 192.168.10.254 (CARP VIP)</li>

<li>Network: 192.168.10.0/24</li>

<li>Access: Limited outbound HTTP/HTTPS only</li>

</ul>

</body>

</html>

EOF

  

# Start and enable NGINX

sudo systemctl  start  nginx

sudo systemctl  enable  nginx

  

# Configure firewall

sudo ufw  allow  'Nginx Full'

sudo ufw  --force  enable

```

  

###  Internal Application Server Setup

  

####  Flask API Server Configuration

```bash

# Create Internal App Server VM

VBoxManage createvm  --name  "Internal-AppServer"  --ostype  Ubuntu_64  --register

VBoxManage modifyvm  "Internal-AppServer"  --memory  4096  --cpus  2

VBoxManage modifyvm  "Internal-AppServer"  --nic1  intnet  --intnet1  intnet

```

  

####  Static IP and Python Environment Setup
*(OpenAi's ChatGPT, personal communication, 1/06/2025)*
```bash

# Configure static IP (192.168.1.10)

sudo nano  /etc/netplan/00-installer-config.yaml

  

network:

ethernets:

enp0s3:

addresses:

- 192.168.1.10/24

gateway4: 192.168.1.254  # CARP VIP

nameservers:

addresses: [8.8.8.8, 8.8.4.4]

version: 2

  

sudo netplan  apply

  

# Install Python and dependencies

sudo apt  update

sudo apt  install  python3  python3-pip  python3-venv  -y

  

# Create Flask application

mkdir -p  /opt/cyberlab-api

cd  /opt/cyberlab-api

  

# Create virtual environment

python3 -m  venv  venv

source  venv/bin/activate

  

# Install Flask

pip install  flask  requests

  

# Create Flask application

cat >  app.py  <<  'EOF'

from flask import Flask, jsonify, request

import datetime

import os

  

app = Flask(__name__)

  

@app.route('/health', methods=['GET'])

def health_check():

return jsonify({

'status': 'healthy',

'timestamp': datetime.datetime.now().isoformat(),

'server': 'Internal Application Server',

'ip': '192.168.1.10',

'zone': 'Internal LAN'

})

  

@app.route('/api/system-info', methods=['GET'])

def system_info():

return jsonify({

'hostname': os.uname().nodename,

'system': os.uname().sysname,

'release': os.uname().release,

'network_zone': 'Internal LAN (192.168.1.0/24)',

'security_level': 'High - Internal access only'

})

  

@app.route('/api/database-status', methods=['GET'])

def database_status():

# Simulate database connectivity check

return jsonify({

'database_server': '192.168.1.20',

'status': 'connected',

'last_check': datetime.datetime.now().isoformat()

})

  

if __name__ == '__main__':

app.run(host='0.0.0.0', port=5000, debug=False)

EOF

  

# Create systemd service

sudo tee  /etc/systemd/system/cyberlab-api.service  >  /dev/null  <<  'EOF'

[Unit]

Description=CyberLab Internal API Server

After=network.target

  

[Service]

Type=simple

User=ubuntu

WorkingDirectory=/opt/cyberlab-api

Environment=PATH=/opt/cyberlab-api/venv/bin

ExecStart=/opt/cyberlab-api/venv/bin/python app.py

Restart=always

  

[Install]

WantedBy=multi-user.target

EOF

  

# Change ownership and start service

sudo chown  -R  ubuntu:ubuntu  /opt/cyberlab-api

sudo systemctl  daemon-reload

sudo systemctl  start  cyberlab-api

sudo systemctl  enable  cyberlab-api

```

  

###  Database Server Setup

  

####  MySQL Server Installation
*(OpenAi's ChatGPT, personal communication, 1/06/2025)*
```bash

# Create Database Server VM

VBoxManage createvm  --name  "Database-Server"  --ostype  Ubuntu_64  --register

VBoxManage modifyvm  "Database-Server"  --memory  4096  --cpus  2

VBoxManage modifyvm  "Database-Server"  --nic1  intnet  --intnet1  intnet

  

# Configure static IP (192.168.1.20)

sudo nano  /etc/netplan/00-installer-config.yaml

  

network:

ethernets:

enp0s3:

addresses:

- 192.168.1.20/24

gateway4: 192.168.1.254

nameservers:

addresses: [8.8.8.8, 8.8.4.4]

version: 2

  

sudo netplan  apply

  

# Install MySQL Server

sudo apt  update

sudo apt  install  mysql-server  -y

  

# Secure MySQL installation

sudo mysql_secure_installation

```

  

####  Database and Sample Data Creation
*(OpenAi's ChatGPT, personal communication, 1/06/2025)*
```sql

-- Connect to MySQL as root

sudo mysql -u root  -p

  

-- Create CyberLab database

CREATE  DATABASE  cyberlab_db;

  

-- Create application user

CREATE  USER 'cyberlab_user'@'192.168.1.%' IDENTIFIED BY  'SecureDBPassword123';

GRANT  SELECT, INSERT, UPDATE, DELETE  ON cyberlab_db.*  TO  'cyberlab_user'@'192.168.1.%';

  

-- Use the database

USE cyberlab_db;

  

-- Create sample tables

CREATE  TABLE  network_zones (

id INT AUTO_INCREMENT PRIMARY KEY,

zone_name VARCHAR(50) NOT NULL,

network_range VARCHAR(20) NOT NULL,

security_level ENUM('Low', 'Medium', 'High') NOT NULL,

description  TEXT,

created_at TIMESTAMP  DEFAULT CURRENT_TIMESTAMP

);

  

CREATE  TABLE  security_events (

id INT AUTO_INCREMENT PRIMARY KEY,

event_type VARCHAR(50) NOT NULL,

source_ip VARCHAR(15) NOT NULL,

destination_ip VARCHAR(15) NOT NULL,

severity ENUM('Low', 'Medium', 'High', 'Critical') NOT NULL,

description  TEXT,

timestamp  TIMESTAMP  DEFAULT CURRENT_TIMESTAMP

);

  

-- Insert sample data

INSERT INTO network_zones VALUES

(1, 'DMZ', '192.168.10.0/24', 'Medium', 'Demilitarized Zone for public services', NOW()),

(2, 'Internal LAN', '192.168.1.0/24', 'High', 'Internal corporate network', NOW()),

(3, 'Management', '192.168.30.0/24', 'High', 'Network management and monitoring', NOW());

  

INSERT INTO security_events VALUES

(1, 'Port Scan', '192.168.10.50', '192.168.1.10', 'Medium', 'Multiple port connection attempts detected', NOW()),

(2, 'Failed Login', '192.168.10.100', '192.168.30.10', 'High', 'Multiple SSH login failures', NOW()),

(3, 'Firewall Block', '10.0.2.100', '192.168.10.10', 'Low', 'Blocked connection from external IP', NOW());

  

-- Configure MySQL for network access

-- Edit /etc/mysql/mysql.conf.d/mysqld.cnf

-- Change bind-address = 127.0.0.1 to bind-address = 192.168.1.20

  

FLUSH PRIVILEGES;

EXIT;

  

# Restart MySQL

sudo systemctl restart mysql

```

  

###  Wazuh SIEM Setup

  

####  Wazuh Server Installation

```bash

# Create Wazuh Server VM

VBoxManage createvm  --name  "Wazuh-SIEM"  --ostype  Ubuntu_64  --register

VBoxManage modifyvm  "Wazuh-SIEM"  --memory  8192  --cpus  4

VBoxManage modifyvm  "Wazuh-SIEM"  --nic1  intnet  --intnet1  mgmtnet

  

# Configure static IP (192.168.30.10)

sudo nano  /etc/netplan/00-installer-config.yaml

  

network:

ethernets:

enp0s3:

addresses:

- 192.168.30.10/24

gateway4: 192.168.30.254

nameservers:

addresses: [8.8.8.8, 8.8.4.4]

version: 2

  

sudo netplan  apply

  

# Install Wazuh using official installation script

curl -sO  https://packages.wazuh.com/4.7/wazuh-install.sh

sudo bash  wazuh-install.sh  -a

  

# Save the generated passwords displayed during installation

# Access Wazuh dashboard at: https://192.168.30.10

# Default user: admin

```

  

####  Wazuh Agent Configuration

  

Install Wazuh agents on monitored systems to centralize logging:

  

```bash

# On each server (DMZ, App Server, Database Server):

  

# Download and install Wazuh agent

wget https://packages.wazuh.com/4.x/apt/pool/main/w/wazuh-agent/wazuh-agent_4.7.0-1_amd64.deb

sudo dpkg  -i  wazuh-agent_4.7.0-1_amd64.deb

  

# Configure agent to connect to Wazuh manager

echo  'WAZUH_MANAGER="192.168.30.10"'  | sudo tee  -a  /var/ossec/etc/ossec.conf

  

# Start and enable agent

sudo systemctl  daemon-reload

sudo systemctl  enable  wazuh-agent

sudo systemctl  start  wazuh-agent

```

  

####  pfSense Syslog Configuration

  

Configure pfSense firewalls to send logs to Wazuh:

  

1.  **Navigate to Status → System Logs → Settings**

2.  **Remote Logging Options:**

```

☑ Enable Remote Logging

Remote Log Servers: 192.168.30.10:514

Remote Syslog Contents: Everything

```

  

---

  

##  5. Testing & Validation

  

###  Connectivity Testing Matrix

  

####  Basic Network Connectivity Tests

  

**Test 1: Internet Connectivity from All Zones**
*(OpenAi's ChatGPT, personal communication, 1/06/2025)*
```bash

# From DMZ Web Server (192.168.10.10)

ping -c  4  8.8.8.8

curl -I  https://www.google.com

  

# From Internal App Server (192.168.1.10)

ping -c  4  8.8.8.8

curl -s  http://192.168.1.10:5000/health

  

# From Database Server (192.168.1.20)

ping -c  4  8.8.8.8

mysql -u  cyberlab_user  -p  -e  "SELECT COUNT(*) FROM cyberlab_db.network_zones;"

```

  

**Expected Results:**

- All servers should reach internet

-  App server API should respond with JSON health status

-  Database should return count of 3 network zones

  

**Test 2: Inter-Zone Access Control Validation**
*(OpenAi's ChatGPT, personal communication, 1/06/2025)*
```bash

# From DMZ → Internal LAN (Should FAIL)

# On DMZ Web Server:

ping -c  4  192.168.1.10  # Should timeout

telnet 192.168.1.10  5000  # Should be blocked

  

# From Internal LAN → DMZ (Should FAIL by default)

# On App Server:

ping -c  4  192.168.10.10  # Should timeout

  

# From Management → All Networks (Should SUCCEED)

# On Wazuh Server:

ping -c  4  192.168.1.10  # Should succeed

ping -c  4  192.168.10.10  # Should succeed

ssh ubuntu@192.168.1.20  # Should succeed

```

  

**Expected Results:**

- DMZ cannot reach Internal LAN

-  Internal LAN cannot reach DMZ (unless specifically allowed)

- Management network can reach all zones

  

###  High Availability Testing Scenarios

  

####  Test 3: CARP Failover Validation

  

**Primary Firewall Shutdown Test:**
*(OpenAi's ChatGPT, personal communication, 1/06/2025)*
```bash

# Step 1: Verify current CARP status on both firewalls

# On Primary pfSense console:

ifconfig | grep carp

# Look for MASTER status on CARP interfaces

  

# On Secondary pfSense console:

ifconfig | grep carp

# Look for BACKUP status on CARP interfaces

  

# Step 2: Verify client connectivity before failover

# From any client (e.g., App Server):

ping -c  10  192.168.1.254  # CARP VIP - should respond

traceroute 8.8.8.8  # Note the gateway path

  

# Step 3: Shutdown primary firewall

# On Primary pfSense:

shutdown -h  now

  

# Step 4: Monitor failover on clients

# From App Server (continue previous ping):

ping -c  20  192.168.1.254  # Should continue responding after brief pause

traceroute 8.8.8.8  # Path may change during failover

  

# Step 5: Verify CARP status on secondary

# On Secondary pfSense console:

ifconfig | grep carp  # Should now show MASTER status

```

  

**Expected Failover Behavior:**

-  **Failover Time**: < 3 seconds for CARP promotion

-  **Service Continuity**: Established connections should survive

-  **New Connections**: Should route through secondary firewall

  

**Test 4: pfSync State Synchronization**
*(OpenAi's ChatGPT, personal communication, 1/06/2025)*
```bash

# Step 1: Generate connection state on primary

# From DMZ Web Server, create HTTP connection:

curl -s  http://192.168.10.10  &  # Background connection

  

# Step 2: Check firewall state table

# On Primary pfSense:

pfctl -s  state  | grep 192.168.10.10

  

# On Secondary pfSense:

pfctl -s  state  | grep 192.168.10.10  # Should show same states

  

# Step 3: Perform failover during active connections

# Shutdown primary while connection is active

# Connection should continue through secondary

```

  

####  Test 5: Configuration Synchronization Validation

  

**XMLRPC Sync Test:**
*(OpenAi's ChatGPT, personal communication, 1/06/2025)*
```bash

# Step 1: Create new firewall rule on primary

# Via pfSense WebGUI on Primary (192.168.1.1):

# Navigate to Firewall → Rules → LAN → Add

# Create rule: Allow TCP from LAN to DMZ port 8080

  

# Step 2: Verify automatic sync to secondary

# Via pfSense WebGUI on Secondary (192.168.1.2):

# Navigate to Firewall → Rules → LAN

# New rule should appear automatically within 30 seconds

  

# Step 3: Test rule functionality

# From App Server:

telnet 192.168.10.10  8080  # Should connect (if service exists)

```

  

###  Security Monitoring Validation

  

####  Test 6: Wazuh Log Collection and Alerting

  

**Firewall Log Integration:**
*(OpenAi's ChatGPT, personal communication, 1/06/2025)*
```bash

# Generate security events for monitoring:

  

# Step 1: Generate port scan from DMZ

# On DMZ Web Server:

nmap -sS  192.168.1.10  # Should be blocked and logged

  

# Step 2: Generate failed authentication attempts

# On any system:

ssh baduser@192.168.30.10  # Multiple failed attempts

  

# Step 3: Check Wazuh dashboard

# Access https://192.168.30.10

# Navigate to Security Events

# Verify events appear with proper categorization

```

  

**Database Connectivity Monitoring:**
*(OpenAi's ChatGPT, personal communication, 1/06/2025)*
```bash

# Step 1: Test database connection from app server

curl http://192.168.1.10:5000/api/database-status

  

# Step 2: Simulate database failure

# On Database Server:

sudo systemctl  stop  mysql

  

# Step 3: Verify monitoring detects failure

curl http://192.168.1.10:5000/api/database-status  # Should show error

# Check Wazuh for database service alerts

```

  




 


