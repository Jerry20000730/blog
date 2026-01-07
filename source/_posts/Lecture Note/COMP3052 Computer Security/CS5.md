---
title: COMP3052 Computer Security 3 -- Authentication
tags: [COMP3052 Computer Security]
categories: COMP3052 Computer Security
cover: images/CS/Part3/cover.png
excerpt: Authentication is a term that refers to the process of proving that some fact or some document is genuine. In computer science, this term is typically associated with proving a user's identity.
---

<!-- toc -->

# Lecture 4 Authentication

Authentication ensures:

- Someone are permitted to access the asset
- Someone are who they say they are

understandCredentials:

- Something they **are**
- Something they **have**
- Something they **know**

## Username & Password

1. First step: identification - you announce who you are
2. Second step: authentication - you prove that you are who you claim to be.
3. Time of check to time of use (TOCTTOU) 这是一个软件设计上的竞争条件
   1. 解决方法：The operating system checks a user's identity at the start of a session but uses the identity to make access control decisions later on during the session

### Password
Problems with passwords:

- Forget
- Guess
- Spoofing and phishing
- Comprimised password files
- Keylogging
- Weak password

### <font color="#FFA500">\[UNDERSTAND\]</font> Password Policies

- Certain length, certain types of characters / No dictionary words / Change regularly / No previously used passwords

### Storing passwords

- Use a one-way hash function

![one-way hash function](/images/CS/Part3/one-way-hash.png)
Password / Shadow Files that stored passwords are read protected

### Cracking Passwords

- Offline: you have a copy of the password hash locally
   - try lots of possible passwords and see if we have a hash collision with a password list
      - brute force
      - dictionary attack
- Online: you do not have the hash, and are instead attempting to gain access to an actual login terminal
   - usually attempted with phishing

#### <font color="#FFA500">\[UNDERSTAND\]</font> Password Salting (improve security)
![password salting](/images/CS/Part3/password-salting.png)

- Cracking multiple passwords is slower - a hit is for a single user
- Prevents rainbow table attacks (reverse hash directly)
- But salting has no effect on the speed of cracking a single password.

## Multi-Factor Authentication

- Password are something you **know**
- **MFA conbines something you know with something you have**
   - Text code to mobile
   - Google/Microsoft Authenticator
   - One time password
   - USB devices

### Biometrics
Combine something you **know** with something you **are**

- Fingerprint
- Retina recognition
- Voice...

Accuracy of a biometric system is important:

- FP rate
- FN rate