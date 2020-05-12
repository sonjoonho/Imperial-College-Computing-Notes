# 09 - TLS

## Transport Layer Security (TLS)

TLS is a cyrptographic protocol to provide confidentiality and integrity of network communications at the *session* layer. As a result, an eavesdropper can only see encrypted information, and MITM tampering leads to integrity checks failure.

A TLS server needs a certificate stating its **identity** and **public key**.

### Certificate Chains

## Server Name Indidcation

SNI is a way to have many domains under a single a certificate. **ENSI** allows for encryption of SNI.

## Certificate Validation

### Extended Validation

This is used to veritfy the identity of an owner or of an organisation. It's common to do this offline: phone, in person, via lawyer. This is more expensive.

### Domain Validation

This is more common, and primarily uses internet-based validation. e.g. CA generates a random token and then

- Owner places token in DNS record for domain
- Owner serves token at a specific URL from domain
- Owner included token in fresh TLS certificate served from domain
- CA emails token to owner at an address under that domain, who submits challenge to CA online

### Threats

Attackers can comprimise DV process and obtain rogue certificates. For example

- IP spoofing: fake DNS reply
- DNS hijacking: serve challenge from malicious IP
- Email snooping: read challenge from cleartext email

To mitigate this, we can use DNSSEC and encrypted emails. This is an example of "threat transfer".

## Mitigating Rogue Certificates

### DANE

DANE is a way to transfer threat to DNSSEC by relying on DNSSEC to control trust in TLS certificates. Then the trust moves from CAs to DNS operators.

## TLS Security Issues

- TLS leaks information via traffic analysis.
- OpenSSL implementation bugs.
- Protocl downgrade attacks

## TLS 1.2 to 1.3

TLS 1.2 uses 3 RTs to establish a system and 2 RTs to resume from accidental interruption which is not efficient.

TLS 1.3 uses one less RT in each case.

## TLS 1.3

- Weak crypto suites not allowed.
- Encrypted handshake.
- ENSI makes it possible to encrypt SNI.

## Malicious TLS

Malware uses TLS, so you can fingerprint malicious packet signatures. This can be used by IDSs.