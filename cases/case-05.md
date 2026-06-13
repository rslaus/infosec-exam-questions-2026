# Case 5

Choosing good passwords and remembering them all may be a hard task for the human mind. Design the security architecture for a password vault service in a public cloud environment (provided by some cloud service provider).

**What are the most essential security services? What security mechanisms would you use to implement those services (be sufficiently specific)? How would you secure the access to the service? What could be remaining vulnerabilities? Don't forget to consider system security and protection against malware.**

*Note: I expect you to make a choice and to defend this choice. Don't present a range of possible solutions.*

## Answer

### Most essential security services:

- confidentialty: noone should be able to read your passwords (evidently)
- authentication: you should be able to authenticate yourself if you want access to your passwords
- data integrity: you don't want your passwords tampered with
- availabilty: your password should always be available

[source: brain]

### Security mechanisms:

- a master key is derived from a password using bcrypt, which is a hash function derivation of blowfish, resistant to brute force search attacks
- the vault data itself encrypted with AES-256-GCM using the master key, 256 bits is the best AES offers and galois counter mode ensures both confidentiality and authentication

  since the blob is encrypted client-side, so a malicious cloud provider can't read it

  the blob should include a timestamp to combat a possible replay attack
- authentication to the server using a username and 2FA, using time-based one-time passwords (TOTP)
- to update the passwords, the client just sends the encrypted blob

[source: claude opus as a starting point, brain, https://en.wikipedia.org/wiki/Bcrypt, ppt 2.2.1 (for AES), ppt 2.2.3 slide 70 (for GCM), https://en.wikipedia.org/wiki/Time-based_one-time_password, claude sonnet sanity check]

### Securing the access:

TLS 1.3 should be used to further encrypt the blob while it's being transported using TCP.

Because of 2FA I don't think any extra security mechanisms are needed. If a hacker bypasses the 2FA, which is already very hard, they get the encrypted blob. They still need to crack the master key in order to decrypt.

[source: brain, claude sonnet sanity check]

### Remaining vulnerabilites:

- there's no master key recovery (losing your passwords isn't the end of the world, you can just recover all your passwords individually)
- beware of weak passwords
- beware of fishing attacks to bypass 2FA

[source: brain, https://en.wikipedia.org/wiki/Time-based_one-time_password]

### system security and malware protection

the only code the server runs is verifying the authentication and sending/receiving the blob, so this reduced attacking surface ensures the system is secure and no malware is executed

[source: brain]
