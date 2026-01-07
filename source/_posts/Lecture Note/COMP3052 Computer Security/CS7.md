---
title: COMP3052 Computer Security 5 -- Firewall
tags: [COMP3052 Computer Security]
categories: COMP3052 Computer Security
cover: images/CS/Part5/cover.jpeg
excerpt: A firewall is a network security device that monitors incoming and outgoing network traffic and decides whether to allow or block specific traffic based on a defined set of security rules
---

<!-- toc -->

# OSI Seven Layer Model (for reference)
![](/images/CS/Part5/1.png)

# Firewall brief
1. **Usage**: prevents unauthorised access of packets from one network to another. 

2. **Functions**:
- Implements 'single point' security measures
- Security event monitoring through packet analysis and logging
- Network-based access control through implementation of a rule set.

3. **Location**:
- Network Firewalls: placed between a subnet and the internet
- Host-based Firewalls: placed on individual machines

4. extended concept: **DMZ** (demilitarized zone)
- A demilitarized zone is a small subnet that  separates externally facing services from the internal network
- Firewall is an implementation of DMZ

## Firewall Basic Function

1. Firewall defends a protected network against parties services that should only be available internally (公司内网)
2. Firewalls restrict access from inside to outside services
3. Firewalls can perform Network Address Translation (NAT)

## Disadvantage of Firewall

1. Cannot protect against attacks that bypass the firewall (tunneling)
2. Cannot protect against internal threats or insiders
3. Cannot protect against the transfer of virus-infected programs or files

## Types of Firewall

### Packet Filters
- Location: layer3 (network layer) and layer4 (transport layer)

- Goal: specify which packets are allowed or dropped, based on:

    - Src / Dst IP
    - TCP/UDP port number

The packet filter will be used for both inbound and outbound traffic, and achieved by examining the packet header.

Example implementation of Packet Fileter in Linux: **_IPTABLES_**

### Stateful (dynamic) Packet Filters
- Stateful packet filters understand requests and replies
   - E.g. TCP open sequence (SYN, SYN-ACK, ACK) pattern
- Stateful packet filters can support policies for a wide range of protocols: TCP, FTP, IRC etc

### Proxy Servers
Using a proxy server to represent you to send request to the server. So that it can prevent unwanted connection or malicious behaviours to the actual web server.

![](/images/CS/Part5/proxy.png)

**Issues:**

1. large overhead (send packet to the proxy server first, and then the target web server)

2. more expensive than packet filtering (need a seperate proxy server)

3. Configuration is complex

### NAT (Network Address Translation)
- background: shortage of IP address
- Advantage:
    1. machine is totally hidden from the internet
    2. only established connections are forwarded to your internal machine
    3. prevent unsolicited attacks on random ports

### Why we need host-based firewall if we already have network-based firewall?
- Background: 
    - host-based firewall: firewall installed on endpoint devices: laptop, mobile devices, etc.,
    - Network-based firewall: A network-based firewall is a firewall software installed at the network perimeter, typically **between an organization's internal network and the Internet**.
- Why?
    - Subnet is not secure. Attack may come from internal, not external

## IPTABLES (configures firewall in Linux) 

**Brief:**
- First rule to match is applied
- Rules are organized in chains
- Uses tables to store chains, matches result in a jump, else we check next rule
- Multiple chains can exist in one table

![](/images/CS/Part5/iptables.png)

### Example rules for IPTABLES
```shell
iptables -A INPUT -i eth0 -p tcp --dport 80 -j DROP
```

where:

| Parameter | Meaning                                                      |
| --------- | ------------------------------------------------------------ |
| `-A`      | Appending to specific chains                                 |
| `INPUT`   | Rule in INPUT chain                                          |
| `-i`      | Checking incoming service request                            |
| `eth0`    | Through network adapter 1                                    |
| `tcp`     | Rule applies to TCP protocol instead of UDP                  |
| `--dport` | Destination port (the listening port on this machine)        |
| `-j`      | Jump                                                         |
| `DROP`    | Jump target is DROP, which means to drop all incoming request |

## Packet Filtering Policies (classification)
1. Permissive (Black listing) - allow everything except dangerous service: easy to make mistakes
```bash
# allow all input/forward/output
iptables -P INPUT/FORWARD/OUTPUT ACCEPT
# but restrict incoming connection to the machine ssh services
iptables -A INPUT -p tcp --dport ssh -j DROP
```

2. Restrictive (White listing) - block everything except designated useful services: secure, but easy to DoS yourself
```bash
# drop all input/forward/output
iptables -P INPUT/FORWARD/OUTPUT DROP
# allow outgoing SSH traffic 
iptables -A OUTPUT -p tcp --dport ssh -j ACCEPT
```

## Stateful Packet Filtering Policies
IPTABLES has modules for stateful packet filtering

- Allow incoming / outgoing SSH connections
```bash
iptables -A INPUT -i eth0 -p tcp --dport 22 -m state --state NEW, ESTABLISHED -j ACCEPT
iptables -A OUTPUT -o eth0 -p tcp --sport 22 -m state --state NEW, ESTABLISHED -j ACCEPT
```

### <font color="#FFA500">\[UNDERSTAND\]</font> NEW, ESTABLISHED, RELATED in IPTABLES
- **NEW** — A packet requesting a new connection, such as an HTTP request. 
- **ESTABLISHED** — A packet that is part of an existing connection.
- **RELATED** — A packet that is requesting a new connection but is part of an existing connection.

## Packet Filter Issues & Drawbacks
1. Cannot prevent attacks that exploit application-specific vulnerabilities
2. Cannot support higher-level authentication schemes
3. Easy to accidentally allow or deny packets incorrectly


