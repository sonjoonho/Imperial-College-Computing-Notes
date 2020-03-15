# 20 - Browser Fingerprinting

In **network fingerprinting**, the goal is to detect configuration information is detected in order to identify a system component. 

- Which host is the DNS server?
- What OS is running on each host?
- What services are running on the open ports of a host?

In **browser fingerprinting**, the goal is to recognise the same browser instance across website visits. 

- **Authentication** - send an email if you login from a device it hasn't seen before.
- **Authorisation** - session tokens may include the hash of a browser fingerprint to prevent session hijacking.
- **Access control** - network or service access may be restricted to a particular known device.
- **Tracking, deanonymisation** - if two websites visits show the same fingerprint, then the user is likely to be the same.

## Passive Fingerprinting

When the server receives a HTTP request, TCP/IP parameters including IP address are received. Furthermore some headers directly reveal information

- User-Agent
- Accept
- Accept-Language
- Accept-Encoding

## Active Fingerprinting

Techniques include

- Looking at installed fonts
- Installed plugins
- Browser type - also reported by HTTP headers
- Time-zone
- Screen resolution and colour depth
- JavaScript engine performance
- JavaScript engine conformance to standard
- GPU or graphic drivers

## Trade-offs

### Passive vs Active

| Passive fingerprinting                            | Active fingerprinting                                        |
| ------------------------------------------------- | ------------------------------------------------------------ |
| Cannot be detected or prevented                   | May be detected and prevented                                |
| Does not affect the target                        | Can disrupt the target                                       |
| Can only gather information exposed by the target | Can probe deeper into the target and reveal more precise information |

### Precision vs Stability

| Precision                                                  | Stability                                                    |
| ---------------------------------------------------------- | ------------------------------------------------------------ |
| A good fingerprint should be different for any two devices | a useful fingerprint for a particular device should not change much over time |

Embedding more attributes in a fingerprint is likely to increase precision but decrease stability.

## Countermeasures

- Leak minimal configuration information
  - Blacklist known fingerprinters
  - Rewrite HTTP request to hide sensitive information
  - Disable plugins and JavaScript
  - The drawback is that some sites will just break
- Mimic a target
  - Spoof information to report the fingerprint of a system target of impersonation
  - Constrained by difference between attacker and user device and context (device, IP, etc.)
- Hide in the crowd
  - Spoof information to report a very common fingerprint compatible with user device
- Destabilise fingerprint
  - Spoof information to present a different fingerprint at each visit to the same fingerprinter

## Counter-countermeasures

- **Robustness** now  becomes another parameter to choose fingerprintable attributes.
  - A **robust** attribute is one that is hard to spoof, that impacts usability, that can be cross-validated with other attributes.
  - For example: network latency, IP, screen size
- Attacker needs to avoid inconsistencies in reported data
  - Switch off plugins
    - They provide a way to cross-check information and detect spoofing
  - Spoof HTTP headers consistently with DOM
    - `User-Agent` should be the same as `navigator.userAgent`
  - Avoid implausible configurations
    - e.g. Android browser with huge screen size
- Attacker needs to avoid detection
  - Presence of anti-fingerprinting behaviour is in itself a fingerprintable feature
- Attacker tries to detect activity of fingerprinting code
  - Avoid spoofing results to legitimate requests to improve user experience.