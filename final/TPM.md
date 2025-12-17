**Table of Contents:**
1. Trusted Platform Module (TPM)
2. TPM-based sealed storage
3. Roots of Trust
	1. Attestation Identity Keys (AIKs)
4. TPM Attestation Privacy Issues

_________
## Trusted Platform Module (TPM)

How TMP works (short animation): [Youtube](https://youtu.be/VyxBuM2Al5Q?si=IVqdiWUfo4Y-lULf)

**How TPM certify configurations to others?**
with a digital certificate by signing configuration info.

**Certification chain:**
TPM verifies hardware/OS > OS verifies applications > user is happy

**Measurement**: The resulting hash value of a loaded component.
	`Multiple measurments combined results in CHAIN OF TRUST`


<center>

![[Pasted image 20251216201126.png | 400]]

</center>

#### Hardware-based root of trust
The hardware-based root of trust initiates the ***chain of trust*** by measuring the initial BIOS code.

#### **(1) Trusted boot** 
accumulates a list (L) of measurements for each program executed, but it does not perform any enforcement.
- Contains measurements (hashes) of everything that actually executed
- Just records what happened - no blocking
- "Here's what ran on this system"

**WorkFlow:**
1. Program was allowed by Secure Boot
2. Trusted Boot records: "Program X with hash Y executed"
3. Adds measurement to list L
4. **Never blocks anything**

#### **(2) Secure boot** - (Enforcement - Happens First)
will halt the system if any attempt is made to execute a program that is not on an approved list (L*).
- Contains approved/authorized programs
- Actively blocks anything not on this list
- "Only these programs are allowed to run"

**WorkFlow:**
1.  Program tries to execute
2. Secure Boot checks: "Is this program's hash in L*?"
- **If YES** → Allow execution,  **If NO** → **Halt the system**

> [!NOTE] I skipped how chain of trust in PCR works check the slides

**WHY THIS WORKS?**
- Because hashes are one-way and collision resistant
- Guaranteed integrity
- Everything is logged

___________
## TPM-based sealed storage

#### (1) Sealing and Binding 
Data is encrypted (sealed) and binded to a specific PCR hash chain value, if the PCR reaches that chain and it is authorized, the decryption will happen (it asks the TPM to *unseal* it).
	*For example:* Disk data should only be decrypted when the PCR chai-of-trust reaches the OS.

__________
## Roots of Trust

#### (1) Root of trust for measurement (RTM)
A trusted implementation of a *hash algorithm*, responsible for the *first measurement* on the platform

#### (2) Root of trust for storage (RTS)
Where the Storage Root Key (*SRK*) is stored, and stores other keys.

**How can TPM protect unlimited keys with limited hardware space?**
Using Key Hierarchy, TPM uses a **tree structure** where it only stores **one master key** (SRK) in its protected hardware, then uses that to encrypt all other keys outside the chip.

#### (3) Root of trust for reporting (RTR)
A trusted implementation of shielded location to hold a secret key representing a unique platform identity, the endorsement key (*EK*) [Also called Attestation Identity Keys (AIKs)].
	`Used for “attestation”`

> The Attestation Identity Keys (AIKs) is a Asymmetric key pair. 

**Prerequisites for attestation are:**
1. Verifier understands the attestor’s hardware configuration
2. Verifier is in possession of an authentic public key bound to the hardware root of trust.

________________________________
## TPM Attestation Privacy Issues

#### (1) The Privacy Problem
When you use TPM attestation to prove your system is trustworthy, you **accidentally reveal too much identifying information** that can be used to **track you** across different services.

**Attestation reveals**:
- Exact BIOS version
- Exact OS version and patch level
- All drivers loaded
- All applications running
- Security software installed
- System configuration

**Scenario**: You visit a website anonymously using Tor browser.
Website requests verification:
```
Your system reports:
- Windows 11 Pro, Build 22621.2134
- Dell BIOS version A23
- Specific hardware configuration
- Chrome version 119.0.6045.105
- Antivirus: Norton 360, version 22.23.1
- VPN client: ExpressVPN 12.5.1
```
This creates a **unique fingerprint** of your system. Even though you're using Tor for anonymity, the attestation reveals so much about your specific setup that you can be tracked.

#### (2) Cross-Service Tracking
If you use the same TPM key to attest to multiple different services, those services can **correlate your identity** across all of them.

#### (3) Issue 2: Hardware Key Linkage
Every TPM chip has a **unique Endorsement Key (EK)** burned into it at the factory. This key is used to sign attestations.

### SOLUTION:
#### **Privacy CA (Certificate Authority)**

1. TPM proves identity to trusted Privacy CA (once)
2. Privacy CA issues **anonymous attestation certificates**
3. Use different certificate for each service
4. Services can verify TPM is valid but can't link attestations
