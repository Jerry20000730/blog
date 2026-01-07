---
title: COMP3052 Computer Security 1 -- Intro
tags: [COMP3052 Computer Security]
categories: COMP3052 Computer Security
cover: images/CS/Part1/cover.png
excerpt: What is computer security? What is the history of computer security? Where does computer security come from? Why do we need computer security?
---

<!-- toc -->

# Lecture 1,2 Intro & Motivation
## On Security (past vs now)
Past: security originally focus on systems with single, or few users
Now: security focus on 

- ubiquitous end systems
- interconnected by networks
- danger of attacks from 'un-trustworthy' nodes
- attacks both remotely and locally
## Attacks & Attackers
status quo: 
hackers -> organized crime
website -> personal data harvester
virus -> trojans and DoS
Complexity of system increase -> while our understanding of this cannot

## Security Management

- Protecting assets of an organization is the responsibility of management
   - assets: sensitive information - product plans, customer records
- Security measures must have full support of senior management
- Security awareness programmes (这个不是程序的意思) convey the information (相当于安全培训)
   - ensure every member know:
      - why security is important
      - what is expected of each member
      - which good practices should follow
- User is not enemy, developers need more awareness (开发人员也要安全培训)、

### Security Policy

1. definition: a statement that defines the security objectives of an organization
   1. what needs to be protected
   2. how this should be achieved
2. understandcontent / objectives:
   1. **Security Policy Objective**(安全策略目标): 是一个用于保护可识别资源(protect identifiable resource)，防止未授权使用(unauthorized use)的声明
   2. **Organisational Security Policy**(有组织的安全策略): The set of laws, rules, and practices that regulate how an organization manages, protects and distribute resources to achieve specified security objectives (为了确保组织管理达到特定的安全策略目标)
   3. **Automated Security Policy**(自动的安全策略): The set of restrictions and properties that specify how a computing system prevents information and computing resources from being used to violate an organizational security policy (为了确保计算资源不违背有组织的安全策略)

### <font color="#FFA500">\[UNDERSTAND\]</font> Measuring security
Measures only exist for some aspects of security - how?

1. **Product Security**: number of security flaw (bugs), the number of interfaces to outside callers - 有争议的
2. **System Security**: number of accounts with system privileges or the number of accounts with weak passwords, number of open ports, service accessible from outside, running system has vulnerability
3. **Cost of an Attack**: 
   1. 在攻击的时候，攻击者需要投入的时间，比如找漏洞
   2. 在攻击的时候需要的花费（计算周期+特殊装备）
   3. 攻击需要的知识(knowledge)
4. **Cost of Assets**

## Risk and Threat Analysis
IT risk analysis can be applied in:

- All information asset
- IT infrastructure
- development of a product or system

Risk - Possibility of an incident or attack to cause damage to your enterprise, informal calculation:

$Risk = Assets \times Vulnerabilities \times Threat$

### Asset
In an IT system, assets include:

- hardware: laptops, servers, routers ...
- software: applications, operating systems, database management systems, source code...
- data and information - essential data for running and planning your business, design documents, digital content, data about your customer
- reputation

Features:

- Identification of assets is easy and straightforward
- Measurement of asset values is a challenge
   - data, information, and reputation is more difficult to measure

### Vulnerabilities

1. Definition: weaknesses of a system that could be accidentally or intentionally exploited to damage assets
2. Typical vulnerability include:
   1. badly configured privileged account
   2. program with unnecessary privileges
   3. programs with known flaws
   4. weak access control
   5. weak firewall configuration

### Threats

1. Definition: actions by adversaries who try to exploit vulnerabilities to damage assets
2. Ways of identifying threats
   1. Categorisation by damage done to assets:
      1. spoofing identities(欺骗身份认证)
      2. Tampering with data(数据篡改)
      3. Repudiation(抵赖): 用户否认执行了带有攻击性的操作
      4. Information disclosure
      5. Denial of Service(拒绝服务攻击)
      6. Elevation of privilege(提高特权)
   2. Identify threats by source
      1. who? - member of organization or an outsider? a contractor or a former member
      2. access the system directly or remotely?
   3. Analysis of attack execution (Attack graphs)![attack graphs](/images/CS/Lecture1_2/attack_graph.png)

### Risk

- <font color="#FFA500">\[UNDERSTAND\]</font> Quantiative Risk Analysis
   - pro: probability theory based on mathematical theory
   - con: 
      - quality of results depends on quality of inputs
      - not always feasible
- <font color="#FFA500">\[UNDERSTAND\]</font> Qualitative Risk Analysis
   - pro: more applicable
   - con: scaling based on judgements of security experts
- Countermeasures - risk mitigation
   - The result of a risk analysis is a prioritized list of threats, together with recommended countermeasures to mitigate risk
   - why risk analysis is not ideal?
      - take time while world outside keeps changing
      - cost high
   - opt for _baseline protection_ as an alternative
      - analyse typical cases and recommends security measures