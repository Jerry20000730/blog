---
title: COMP3052 Computer Security 4 -- Access Control
tags: [COMP3052 Computer Security]
categories: COMP3052 Computer Security
cover: images/CS/Part4/cover.png
excerpt: Access control is a security technique that regulates who or what can view or use resources in a computing environment
---

<!-- toc -->

## <font color="#FF0000">\[IMPORTANT\]</font> Background

To discuss access control, we first have to develop a suitable terminology. The very nature of 'access' suggests that:

- There is an active entity - a _**subject**_ or _**principal**_ 
- Accessing a passive _**object**_ with some specific _**access operation**_
- While a _**reference monitor**_ grants or denies access

![Fundamental Model of Access Control](/images/CS/Part4/authen_author.png)

## Difference between authentication and access control:

- Authentication verify identity
- Access control stipulates who can or cannot access certain resources

## Access control has two steps - Authentication & Authorisation

- Authentication
   - Decide who has access to the system
- Authorisation
   - of those with access, who is authorised to do something to the resource (object)

## Principal vs Subject vs Object

- **Principal**: An entity that can be granted access to objetcs or can make statements affecting access control decisions
   - user identity in an OS, used when discussing security policies
- **Subject**: An active entity within an IT system (active party)
   - process running under a user identity, used when discussing operational system enforcing policies.
- **Object**: Files or resources (passive party)

## Access Operations

- Access Mode
   - Observe: look at the contents of an object
   - Alter: change the contents of an object
- General Model on common access on files:
   - Read (concern: confidentiality)
   - Write (concern: integrity)
   - Execute
- Ownership (in charge of setting security policies)
   - Owner can be defined for each resource, owner controls who gets access
   - Or the security policy is a system-wide mandatory policy 

### <font color="#FF0000">\[IMPORTANT\]</font> Access Control Structures

1. Access Control Matrix (ACM)
![Access control matrix](/images/CS/Part4/acm.png)

At a basic level, access rights can be defined individually for each combination of subject and object simply and object simply in the form of a table. The access control matrix is an **abstract** concept and not very suitable for direct implementation if the number of subjects and objects is large or if the sets of subjects and object change frequently.

2. Capabilities
![Capability](/images/CS/Part4/capability.png)

There are two fundamental options for implementing an access control matrix. Access rights can be kept with the subjects or with the objects. In the first case, every subject is given a _capability_, an unforgeable(不可伪造的) token that specifies this subject's access rights. This capability corresponds to the subject's row in the access control matrix

**Disadvantage**

- It is difficult to get an overview of who has permission to access a given object
- It is difficult to revoke(撤销) a capability - either the operating system has to be given the task or users have to keep track of all the capabilities they have passed on

3. Access Control Lists (ACL)
![Access control list](/images/CS/Part4/acl.png)

- Less memory used
   - Stored with a file and quick to access
   - but:
      - management of individual subjects is cumbersome
      - obtaining an overview of permissions is challenging
      - tedious to set this up for every subjects and objects
   - UNIX simplifies the ACL structure: User/Group/others
   - Windows extends usual rwe with:
      - take ownership
      - change permission
      - delete

## Intermediate Controls

Problems of complexity are solved by indirection. This principle can be applied to access control. Intermediate layers between users and objects are introduced to represent policies in a more manageable fasion.

### Groups
![Groups](/images/CS/Part4/group.png)
Users with similar access rights are collected in groups and groups are given permissions to access objects, and are thus a mean of simplifying the definition of access control policies. 

### Negative Permission
![Negative Permission](/images/CS/Part4/negative_permission.png)
an entry in an access control structure that specifies the access operations a user is not allowed to perform.

**Policy Conflict**

In the above image, the negative permission given to **u3** contradicting the positive permission given to group **g1**, and thus an example of policy conflict.

- Can be resolved by reference monitor
    - e.g., a simple and widely used algorithm is just process the first entry matching in ACL(Access Control Lists) and ignore the later entries

### Privileges
![Privileges](/images/CS/Part4/privileges.png)

A policy could refer to the operationss a user is allowed to execute. _Privilege_ stands for the right to execute certain operations

- Usually associating with operating system functions

### Robe-based Access Control (RBAC)

Compared with ACL: Identity-based Access Control (IBAC)

- Access is based on a role, e.g. accountants should access certain financial files
   - role -> collection of application specific operations or resource access
      - Difference with group and role:
         - group are collections of users
         - roles are collections of permissions
   - RBAC focuses on users and the jobs they perform
   - Easier to scale and applicable to large networks and organizations (enterprise level)
   - Most operating systems are user/group based, so role-based access can be provided using nested groups![rbac within groups](/images/CS/Part4/rbac.png)

### Protection Rings

![protection rings](/images/CS/Part4/protection_ring.png)

Protection rings are a particularly simple example of an intermediate layer of hardware-based access control for subjects and objects. Each subject (process) and each object is assigned a number, depending on its 'importance'.

Access control decisions are made by comparing the subject's and object's numbers

## Comparing Security Attributes
When evaluating a security policy, he reference monitor compares the access rights granted to the subject with the access rights demanded by the policy. 

### Partial Ordering
A security policy might grant access to an object if the object label is $\le$ subject label

![](/images/CS/Part4/partial_ordering.png)

Considering the above example, the department creates a group _Year\_1_ for first year students to manage access for resources specifically dedicated to them. There is also a group _Year\_2_ for second year students, _Year\_3_ for third year students, etc. The group of first year students would be contained in the group of all students, but there is no such relation between group _Year\_1_ and _Year\_2_. By using **_Hasse diagrams_** (a graphical representation of partially ordered sets in the above image), the edges in the diagram give a seleton of the partial ordering, which is $a \le b$ if and only if there a path from a to be. 

In this case, $\{Year\_1\} \le \{Year\_1, Year\_2\}$, and a security policy might grant access to an object if the object label is $\le$ subject label.

### Multi-Level Security
![multi-level security](/images/CS/Part4/multi-level-security.png)

Linearly ordered hierarchy of four security levels in early security research in 1970s and 1980s: often in military applications.

### Lattice
The linear ordering of security levels in Multi-Layer security can only express a limited security policies, while in order to be able to state other policies, such as people at level _secret_ could have access to lower categories, a **lattice** of security levels was introduced.

![Lattice](/images/CS/Part4/lattice.png)

### Others
1. JVM: sandboxing - run code in restricted environments
2. Virtualization - emulates a number of other machines
3. Trusted computing - verifiable state of software and hardware





