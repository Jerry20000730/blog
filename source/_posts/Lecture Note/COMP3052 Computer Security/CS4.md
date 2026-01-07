---
title: COMP3052 Computer Security 2 -- Computer Security Foundations
tags: [COMP3052 Computer Security]
categories: COMP3052 Computer Security
cover: images/CS/Part2/cover.jpeg
excerpt: Security is about the protection of assets, computer security deal with the protection of IT assets.
---

<!-- toc -->

# Lecture 3 Foundations

## Key concept

### Security
- Security is about the protection of assets
- Knowledge of assets and their values are vital
- Protection measures:
   - prevention: taking measures that prevent your assets from being damaged
   - detection: taking measures that allow you to detect when an asset has been damaged, how it has been damaged, and who has caused the damage
   - reaction: taking measures that allow you to recover your assets or to recover from damage to your assets

### Computer Security

- Definition: Deals with the prevention and detection of unauthorised actions by users of a computer system
- Six aspects (<font color="#FF0000">\[IMPORTANT\]</font> three main: CIA):
   - **Confidentiality** (prevention of unauthorised disclosure of information)
      - privacy - protection of personal data
      - secrecy - protection of data of an organization
   - **Integrity** (prevention of unauthorised modification of information)
      -  deals with the prevention of unauthorised writing
      - assurance that data remains unmodified
   - **Availability** (prevention of unauthorised withholding of information or resources)
      - The property of being accessible and usable upon demand by an authorised entity
      - Denial of Servie (DoS) is a way of prevention of availability, or availability wish to prevent DoS.
         - Definition of DoS: The prevention of authorized access to resources or the delaying of time-critical operations
   - <u>Accountability</u> (Users should be held responsible for their actions)
      - System should **identify** and **authenticate** users
         - audit information must be selectively kept and protected so that actions affecting security can be traced to the responsible party
   - <u>Non-repudiation</u> (不可否认性)
      - provide unforgeable evidence (不可欺骗的证据)
         - evidence that is verifiable by a third party
      - Two way
         - nonrepudiation of origin - sender identification
         - nonrepudiation of delivery - delivery confirmation
   - <u>Reliability</u>
      - against (accidental failure)
      - difference with safety: impact of system failures on their environment
      - 统一概念：可信任性(dependability)，将safety, reliability, integrity and availability作为dependability的几个方面
         - Dependability: the property of a computer system such that reliance can justifiably be placed in the service it deliver

## Computer Security Fundamental Dilemma

- Security-unaware users have specific security requirements but usually no security expertise
   - trade-off between security and ease of use
      - Security mechanism need increased computational resources
      - Security interferes with working patterns of users
      - Managing security is tired - purchasers often choose best GUI

## Data vs Information
Security is about controlling access to information and resources, but this can be difficult, thus controlling access to data is more viable.

- Data 		- Means to represent information	
- Information 	- (Subjective) Interpretation of data

### Problem of inference
focusing on data can still leave information vulnerable

- Carefully chosen queries can narrow down who has what condition

## <font color="#FF0000">\[IMPORTANT\]</font> 3.4 Security Design: Principles

- Focus of Control
- Complexity vs. Assurance
- Centralised or Decentralised Controls
- Layered Security

### Focus of Control (1st Design Decision)
should protection mechanism focus on:

- data,
- operations,
- or users?

### Complexity vs Assurance (2nd Design Decision)
Do you prefer simplicity and higher assurance to a feature-rich security environment?

### Centralised or Decentralised Controls (3rd Design Decision)
Should the tasks of defining and enforcing security be given to a central entity or be left to individual components in a system?
central - could mean bottleneck
distributed solution - more efficient but harder to manage

### The layer Below (4th Design Decision)
How can you prevent an attacker getting access to a layer below the protection mechanism?

- A good security layer built upon an insucure layer is useless.