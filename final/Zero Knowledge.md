**Table of Contents:**
1. P and NP
2. Interactive Proof (IP) systems
3. ZK proofs for NP
4. ZK Proofs As Signatures
______
## P and NP
| **Class**  | **Definition**                                                               | **Simple Explanation**                                                                                                   | **Examples**                                                                                                                                                                                                                                                                                    |
| ------ | ------------------------------------------------------------------------ | -------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **P**  | Problems that can be **solved** easily (in polynomial time)              | If you give me the problem, I can **find the answer** quickly                                                        | • Sorting a list of numbers<br>• Finding shortest path on a map<br>• Checking if a number is even<br>• Searching for a name in a phone book                                                                                                                                                 |
| **NP** | Problems whose solutions can be **verified** easily (in polynomial time) | If you give me a **proposed answer**, I can **check if it's correct** quickly (but finding the answer might be hard) | • Sudoku puzzle (hard to solve, easy to check)<br>• Factoring large numbers (hard to find factors, easy to verify by multiplication)<br>• Travel salesman route (hard to find best route, easy to check distance)<br>• Password cracking (hard to find password, easy to verify if correct) |
## Interactive Proof (IP) systems
A conversation where the Prover convinces the Verifier through back-and-forth questions 
	`This is for P`

**Not Interactive (old way)**:
- Prover gives complete answer
- Verifier checks entire thing
- One-shot, no conversation

**Interactive (new way)**:
- Prover and Verifier have a dialogue
- Verifier asks random questions
- Prover responds
- Multiple rounds of back-and-forth

**TO BE INTERACTIVE PROOF one must have these two conditions:**

1. **Completeness**: The prover CAN solve the challenge, the verifier believes with probability (2/3).

2. **Soundness**: The prover has LOW probability (1/3) of fooling the verifier.

_______________
## ZK proofs for NP
	commitment schemes

**P problems**: Easy to solve → Easy to verify → No need for commitment

**NP problems**: Hard to solve → Easy to verify → Need commitment to prevent cheating because they cant solve it themselves
#### General Structure
- A -> B : Commitment 💍
- A <- B : Challenge 🥊
- A -> B : Response ✅

> The challenge phase could be multiple rounds to ensure there is no probability of cheating or guessing

Analyzing the protocol from slide 31.

<center>

![[Pasted image 20251216223401.png | 400]]

</center>

***Aim:***

**Alice knows**: The secret exponent s **Bob wants to verify**: Alice really knows s, without learning what s is.

***QUESTIONS:***

**(1) In Step (1) why randomness?**
Makes it zero-knowledge. Bob will never learn the actual secret s because it's masked by r.

**(2) How does Verification work?**

Bob computes: g^z · y^(-e)
            = g^(r + se) · (g^s)^(-e)       [substituting z = r+se and y = g^s]
            = g^r · g^(se) · g^(-se)       [expanding]
            = g^r                                  [se terms cancel out]
            = c                                   [this equals Alice's commitment!]

> **If it matches**: Bob is convinced Alice knows the secret s 

**(3) How does it have Soundness?** 
To cheat Alice needs to break secret logarithmic, she cant. 


**(4) Can this protocol be made non-interactive?**
*YES*, Alice sends the following in one go:

> `C = g^r, e = H(g^r), z = r+se`

**(5) What This Protocol Actually Proves**
The entity I'm talking to knows the secret s that corresponds to key y.
So it *Prevents Impersonation* (of the key holder):
```java
Alice's session at 2pm: Uses s
Attacker at 3pm: Doesn't have s → Cannot impersonate ✗

Bob knows: "3pm requester is NOT the same as 2pm requester"
```

_______________
## ZK Proofs As Signatures
This can be done by replacing Verifier’s interactive challenge with the hash of the message to be signed concatenated with the commitment.

> `Challenge = Hash (Message || Commitment)`

**Creating Signature (Schnorr’s signature):**
private key is x and her public key is y=gx

1. Alice sends a commitment, r = g^k to Bob, where k is an random number.
2. Alice computes h = Hash(m || r).
3. Alice sends s = k +hx (mod q).
4. The signature on the message m is simply the pair (r, s)

**Verifying Signature (Schnorr’s signature):**

<center>

![[Pasted image 20251216225911.png | 200]]

</center>
