**Table of Contents:**
1. Shamir's Secret Sharing (N, d) Threshold
2. Verifiable Secret Sharing (VSS) - Defense Against Modification
3. Summary of Attack Scenarios

__________
## Shamir's Secret Sharing (N, d) Threshold

**For threshold d**:
- Pick random polynomial of degree (d-1): f(x) = aₐ₋₁x^(d-1) + ... + a₁x + s (mod p)
- Secret s = f(0)
- Share for user i = f(i)
- Any d users can interpolate polynomial using *Lagrange interpolation*
- Fewer than d users learn nothing

#### Properties

**Perfect security**: With fewer than d shares, ALL values of secret are equally likely

**Flexible**: Can add new users without modifying existing shares

**Equal parties**: All participants have equal power

**Share size**: Same size as the secret

#### Attack Scenarios

**(1) Bad Key Splitting**
**Scenario**: Split 100-bit key, give each party 50 bits
> Each party can do 2^50 exhaustive search (feasible)

**(2) Share Modification**
**Scenario**: Malicious user modifies their share before reconstruction
```python
Setup: Alice, Bob, Carol have shares for secret "Launch Code 123"
Reconstruction time:
  Alice: Sends correct share
  Bob (malicious): Sends wrong share
  Carol: Sends correct share
Result: Wrong secret reconstructed → system fails
```
> Malicious Bob can still recover the CORRECT secret (has valid share)

**(3) Share Interception/Eavesdropping**
**Scenario**: Attacker intercepts shares during distribution or reconstruction
> If attacker gets d or more shares they can reconstruct the secret

**(4) Communication Channel Attack
**Scenario**: Sending secret through multiple "partially trusted" channels

**Setup**:
- Sender splits message using (4, 2) scheme
- Sends f(1), f(2), f(3), f(4) through 4 different channels
- Receiver needs any 2 to reconstruct

**Attack**: Adversary who controls 2 or more channels
- If controls channels 2 and 3 → can reconstruct secret
- If controls only 1 channel → learns nothing

**Protection**: Adversary must compromise ≥d channels to break security

_____________
## Verifiable Secret Sharing (VSS) - Defense Against Modification
	How to detect modified shares?

**Problem**: Malicious participants send bogus shares, to ruin the shares of other participants 
**Solution**: Dealer commits to polynomial coefficients

**How it works**:
1. Dealer publishes: g^s, g^(a₁), g^(a₂), ..., g^(aₐ₋₁) mod p
2. User i receives share f(i)
3. User i verifies: g^f(i) = (g^(aₐ₋₁))^(i^(d-1)) × (g^(aₐ₋₂))^(i^(d-2)) × ... × g^s

>Prevents both malicious users sending wrong shares AND users receiving corrupted shares

___________
#### Summary of Attack Scenarios

| **Attack**                 | **Description**                             | **Impact**                       | **Defense**                                        |
| ---------------------- | --------------------------------------- | ---------------------------- | ---------------------------------------------- |
| **Bad splitting**      | Split key directly (50+50 bits)         | Each party can brute force   | Use proper polynomial scheme                   |
| **Share modification** | Malicious user sends wrong share        | Wrong secret reconstructed   | Verifiable Secret Sharing (VSS)                |
| **Eavesdropping**      | Attacker intercepts (d-1) shares        | No impact (learns nothing)   | Proper threshold d                             |
| **Eavesdropping**      | Attacker intercepts d+ shares           | Secret compromised           | Secure channels, higher threshold              |
| **Compromised dealer** | Dealer creates invalid/different shares | System fails or secrets leak | Trust dealer or use distributed key generation |
| **Channel compromise** | Control d+ communication channels       | Secret intercepted           | Use more channels, higher threshold            |
