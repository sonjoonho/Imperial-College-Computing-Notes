# 02 - Vulnerabilities

**Vulnerabilities** - software bugs that attackers can exploit in order to compromise computers.

**Exploits** - pieces of software that take advantage of a vulnerability in order to access or infect a computer.

**Zero day vulnerability** - A vulnerability that is unknown to the software vendor.

| Actor                   | Action            |
| ----------------------- | ----------------- |
| Vendor's employees      | fix               |
| Security companies      | sell, disclose    |
| Independent researchers | sell, disclose    |
| Academics               | disclose          |
| Government agents       | exploit, disclose |
| Criminals               | exploit, sell     |
| Terrorists              | exploit           |
| Hacktavists             | exploit           |

## Advisories

Security advisories are issued by vendors or security companies, and publicly disclose vulnerabilities.

Each vulnerability is a unique ID e.g. CVE-2014-0160. 

## Ethics

When a vulnerability is discovered:

- **Full disclosure** - make all details public.
  - Preferred by security researchers and the open-source community.
  - May expose users to attack until patched.
  - But attackers may know about the vulnerability anyway.
- **Responsible disclosure** - affected vendor decides when to release information and how much.
  - Preferred by software vendors.
  - Can lead to a long time from discovery to fix.
- **Non-disclosure** - keep the vulnerability a secret.
  - Preferred by parties interested in exploiting the vulnerability, and vendors unwilling to invest resources in fixing them.
  - Based on discredited principle "security by obscurity".