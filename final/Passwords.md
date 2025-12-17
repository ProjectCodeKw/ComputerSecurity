Table of Contents
1. User authentication 
2. Motivation for strong authentication protocols
3. Identification protocols 
	1. Lamport’s hash 
	2. Strong password protocols

_______________

**The four horsemen of authentication:**
1. Something the individual *KNOWS*: Password, PIN
2. Something the individual *POSESES*: Token, Card, Physical key
3. Something the individual *IS*: Biometrics, Voice, Fingerprint
4. Something the individual *DOES*: Handwriting style, Voice pattern, Typing Rhythm 

***Issues of BIOMETRICS:***
1. Obviously user **privacy**, you dont want to share your pupils with the world. 
2. **Accuracy**, Shapeshifter-phobic ;(
3. Biometrics collected for one purpose can be used for another

### Questions

**A) Why use Salt with Hashes?**
It makes brute attack attempts and hash guessing harder (If you cant win make them suffer) -- They will need to brute force EVERY user INDEPENDATLY, even duplicate passwords will look different.
	`To make this better, use multiple hashes with salts inside of each other!`
> (k bits salt increases guessing load by a factor of ~2^k )

**B) How to stop dictionary attacks?**
Use one time passwords.
#### Unix password Scheme
	Password + Salt and thats it

***Rainbow Table Attack***
	 `Attacker PRE-Computes everything (all hashes) a head of time`
	 To combat this: use LARGE salts.

| **Aspect**        | **Dictionary Attack** | **Rainbow Table**      |
| ----------------- | --------------------- | ---------------------- |
| **Computation**   | During attack         | Before attack          |
| **Storage needs** | Low (just passwords)  | Very high (all hashes) |
| **Speed**         | Slower                | Faster                 |
| **Trade-off**     | Time vs Space         | Space vs Time          |
____________
## Identification Protocols
	To combat: Impersonation, Offline attacks and eveasdroping

**ways you can authenticate your passwords:**
1. Diffie Helman key exchange for secure channel
2. SSL secure connection channel
3. One-time passwords (Lamport's Hash)

### 1. One-time password scheme - LAMPORT HASH
	Does not rely on any public key crypto.

It's the decrement hash thingy 
STEPS:
1. Send password to server
2. The password gets hashed multiple times in the initial connection
3. Let n be the number of current hash cycle 
4. In each connection, the user and the server use the correct H_n(password) and both of them keep track of which n they are currently on
5. Decrement n

![[Pasted image 20251216001057.png]]

**Why do we need to add salt to this scheme?**

| **Without Salt**                          | **With Salt**                          |
| ------------------------------------- | ---------------------------------- |
| Same password = Same chain            | Same password ≠ Same chain         |
| Cross-user replay attacks possible    | Each user has unique chain         |
| One compromise affects multiple users | Compromise isolated to single user |
#### 2. Encrypted Key Exchange (EKE) - Strongest one yet
	1. Based on Diffien Hellman AND it does rely on public key crypto!
	2. EKE wants to provide both key establishment and authentication

> **Why use it?**
	- Its Primary purpose is to protect against EVESDROPING when using weak passwords AND it doesnt need trusted authorities

![[Pasted image 20251216003355.png]]

***Protocol Flow:***
1. Alice sends her public key to S
2. Server replies with Response (R) encrypted in Alice's PK
3. Alice replies back with a R encrypted in the shared weak password W

**ISSUES TO SOLVE:**

**(1) Authentication: is the KA coming from Alice or someone impersonating Alice**
- Use Diffie Hellman to obtain PK
- Sign KA with the weak password W

**(2) Someone impersonating the server can do offline dict. attack based on {R}W**
- Yes, based on the weak scheme above they can, so dont use W to encrypt stuff only to authenticate yourself!

***Optimized Protocol:***

![[Pasted image 20251216004132.png]]

**(3) Can someone get KA using Offline Attack?**
>NO, to because KA is public key which is random values that cannot be guessed.

**(4) So whats the point of all this?**
> we use W to authenticate our selves, 
	we use KA to encrypt our session key K, 
		we use K to encrypt our data and nonce.

__________
### 3. Partition Attack
	The Information Leakage Problem in EKE because of RSA

**The Attack**: An attacker can test whether a decryption produces a valid-looking public key without knowing if it's the _correct_ one, utilizing RSA Prime number rule.

**The problem** isn't just keeping the password secret, it's ensuring that **failed decryption attempts leak zero information** about whether the guess was right or wrong.

***SOLUTION***: Do DIFFIE HELLMAN

![[Pasted image 20251216005229.png]]

____________
### 4. Augmented strong password protocols (SRP)
	This tries to fix the problem that both the server and Alice keep a copy of W (What if the server is comprimised?). 

**How this works:**
1. Client: Pick password W
2. Client: Compute v = g^W mod p
3. Client → Server: v
4. Server stores: (Alice, v)  ← NOT the password!

**Authentication:**
1. Client: Knows W
2. Server: Knows v = g^W
3. Protocol proves: Client knows W such that g^W = v
4. But: Server cannot compute W from v *(discrete log problem)*

#### Secure Remote Password (SPR)
DEMO on how SPR Works (Pretty Cool): [Youtube](https://youtu.be/xYGDjTavVNY?si=YB8LskvCwpOT0iZT)

Why use SPR?
- Zero Knowledge property
- Resistance to Small Subgroup Attacks (attacker cannot abuse prime properties)
- Mutual Authentication Built-In