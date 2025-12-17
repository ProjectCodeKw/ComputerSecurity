Table of Contents:
1. Models for Computer Security
2. Bell-LaPadula (BLP)
3. Biba Integrity model
4. Clark-Wilson Integrity Model
5. Chinese Wall Model
6. Model Comparison

___________
## Models for Computer Security
norhing important...

______
## BUBU LADODO PEEPBOB MODEL (BLP)
**"no read up, no write down"** to prevent ***information leakage*** from high security levels to low security levels.

**Security levels:**
1. Top Secret (highest)
2. Secret
3. Confidential
4. Unclassified (lowest)

**Mandatory access control (MAC):**
**ss-property** -> No read up
**\* -property** (star property)  -> No write down -> To prevent information leakage!
**ds-property** -> An individual (or role) may grant to another individual (or role) access to a document based on the owner

#### Incompatibility of Confidentiality and Integrity in BLP ;(

**The Core Problem**
BLP focuses on **confidentiality** (preventing leaks). But it **ignores integrity** (preventing corruption/tampering). When you mix these concerns, BLP breaks down.

#### Attacks:

**(1) Covert Channel Attack**
A high-level process can signal information to a low-level process by manipulating *shared resources* in patterns (like Morse code).

**(2) The Cooperating Conspirator Problem**
- Conspirator 1: Low-level malicious program (Unclassified) 
- Conspirator 2: High-level accomplice (runs at Top Secret level)

**The attack**:
```java
// The trojan horse path
1. Low-level attacker creates a malicious document
2. Document contains hidden malicious program
3. A Top Secret opens/executes this document
4. Now the malicious code is running at Top Secret level
5. The malicious code can read Top Secret data 
6. It uses covert channels through shared resources to broadcast this data to the low-level conspirator!
```
__________________
## Biba Integrity model
The Biba model is the **opposite of BLP**. While BLP protects **confidentiality** (preventing leaks upward), Biba protects **integrity** (preventing corruption from below).

***no write UP, no read DOWN***
This time we consider integrity of the subject!
>To write an object one must have higher integrity: I(S) ≥ I(O) [read is I(S) ≤ I(O)]

**How does BIBA prevent low integrity programs from executing high integrity code?**
Subject S₁ can invoke subject S₂ only if I(S₁) ≥ I(S₂)

<center>

![[Pasted image 20251217052543.png]]

</center>

	`Stolen from Sudo's Homework`

- **C_s ⊆ C_o**: Subject's categories must be **subset of** object's categories
- **C_s ⊇ C_o**: Subject's categories must **include all** of object's categories

______________________
## Clark-Wilson Integrity Model (CWM)
	A more practical integrity mode (Business-focused)
#### The Two Core Concepts

1. **Well-Formed Transaction**
	A **consistent state** means all business rules and constraints are satisfied - the data makes sense and is valid.

2. **Separation of Duty**
	The person who **approves** a transaction cannot be the same person who **executes** it. This *prevents fraud*.

#### Terminologies 
- **CDIs**: constrained data items (loan app; checks): Data subject to integrity controls 
- **UDIs**: unconstrained data items: Unchecked data items. Data not subject to integrity controls. An example is a simple text file.
- **IVPs**: integrity verification procedures:  Procedures that test that CDIs conform to the integrity constraints 
- **TPs**: transformation procedures:  Procedures that take the system from one valid state (change the CDIs) to another
#### The Two Types of Rules
- **Certification rules** (CR): Security policy restrictions that define **what must be true** about IVPs and TPs (Requirements).
- **Enforcement rules** (ER): Built-in **security mechanisms** that make the certification rules actually happen (The actual code).

**Clark-Wilson addresses**:
- Internal fraud and honest mistakes (separation of duty)
- Business rule enforcement (well-formed transactions)
- Audit requirements (all changes logged)
- Approval workflows (certifier ≠ implementer)

>[!NOTE] There is more to memorization in this chapter but im skipping it..

______________
##  Chinese Wall Model
	Developed for commercial applications in which conflicts of interest can arise.

#### Model Components
- **Objects**: items of information related to a company
- **Company dataset (CD)**: contains objects related to a single company
- **Conflict of interest class (CoI)**: contains datasets of companies in competition 
		`Assume: each object belongs to exactly one CoI class`

Introducing... ***\*-property rule*** A subject S can *write* an object O only if:
> S can read O according to the simple security rule, and all objects that S can read are in the same dataset DS as O.

<center>

![[Pasted image 20251216193242.png]]

</center>

#### **Scenario A: Write to Bank A**
- John read from: Bank A, Oil A
- Trying to write to: Bank A
- Check: Are all objects John read from the same DS as Bank A? **No** (he also read Oil A)
- **Result**: **BLOCKED** (unless the report only uses Bank A information)

**Once John accessed Bank A**: He's blocked from Bank B (competitor in same CoI)
**Once John accessed Oil A**: He's blocked from Oil B (competitor in same CoI)

> [!MARK] Sanitized Dataset do not need a wall because its anonymous data

________________________

##  Model Comparison 

| **Aspect**                | **Biba**                                            | **Clark-Wilson (CWM)**                                                                             | **Chinese Wall**                                                                                         |
| ------------------------- | --------------------------------------------------- | -------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------- |
| **Primary Goal**          | Prevent data corruption                             | Ensure transaction correctness and prevent fraud                                                   | Prevent conflicts of interest                                                                            |
| **Focus**                 | Integrity (data trustworthiness)                    | Integrity (business process correctness)                                                           | Confidentiality (information flow control)                                                               |
| **Key Concept**           | **Integrity levels (high/low trust)**               | **Well-formed transactions + separation of duty**                                                  | **Dynamic access control based on history**                                                              |
| **Main Rules**            | • No write up<br>• No read down<br>• No invoke down | • Only certified TPs can modify data<br>• IVPs verify consistency<br>• Separation of duty required | • Simple security: Can't read competing companies<br>• *-property: Can't write if read multiple datasets |
| **Write Restriction**     | Low integrity cannot write to high integrity        | Only through certified TPs (procedures)                                                            | Can't write to dataset if you've read from different datasets                                            |
| **Read Restriction**      | High integrity cannot read from low integrity       | Users can only execute authorized TPs                                                              | Once you read from one company, blocked from competitors                                                 |
| **Access Control**        | Static (based on integrity levels)                  | Procedural (through certified transactions)                                                        | Dynamic (changes based on access history)                                                                |
| **Threat Model**          | Malware corrupting trusted files                    | Fraud, errors, unauthorized modifications                                                          | Insider trading, conflicts of interest                                                                   |
| **Best For**              | Operating systems, military systems                 | Financial systems, databases, business transactions                                                | Consulting, financial services, competitive industries                                                   |
| **Example Scenario**      | Untrusted download can't modify system files        | Bank transfer must go through validated procedure                                                  | Consultant working for Apple can't access Samsung data                                                   |
| **Enforcement Mechanism** | Integrity labels on subjects/objects                | Access control lists + certified procedures + audit logs                                           | Real-time access history tracking                                                                        |
| **Flexibility**           | Rigid                                               | Moderate                                                                                           | High                                                                                                     |
