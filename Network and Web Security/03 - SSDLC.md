# 03 - SSDLC

**S**ecure **S**oftware **D**evelopment **l**ife**c**ycle.

## Threat Modelling

Aims to guide decision making.

- Who are the attackers, what are their goals?
- What attacks are likely to occur?
- What security assumptions does the system rely on?
- Where to invest the security budget?

1. Model the system
2. Identify threats (STRIDE/attack trees)
3. Evaluate and address threats (DREAD, META)

## Model the System

**Data-flow diagrams** show the flow of information across system components.

- External entities are out of control.
- Trust boundaries help establish what principal controls what.
- *Attacks tend to cross trust boundaries.*

![image-20200124220114840](/home/joon/.config/Typora/typora-user-images/image-20200124220114840.png)

## Identify Threats

### STRIDE

For each element in the DFD, ask "what can go wrong?".

- **S**poofing - pretending to be somebody else.
- **T**ampering - modifying without permission.
- **R**epudiation - denying to have done something.
- **I**nformation disclosure - revealing information without permission.
- **D**enial of service - prevent a system from providing service.
- **E**levation of privilege - achieve more than intended.

### Attack Trees

Alternative to STRIDE.

- Root node represents the goal of the attack. 
- Children are steps to achieve the goal.
- Leaves are concrete attacks.

By default, sibling nodes represent step *sufficient* steps (step 1 *or* step 2). Special notation to represent *necessary* steps (step 1 *and* step 2).

![image-20200124221103801](/home/joon/.config/Typora/typora-user-images/image-20200124221103801.png)

## Evaluate Threats

It's difficult to quantify risk, because it's difficult to estimate realistic parameters. The **Black Swan** problem: extremely rare events are hard to predict and quantify.

### DREAD

Score each threat between 5 and 15.

![image-20200124221210232](/home/joon/.config/Typora/typora-user-images/image-20200124221210232.png)

## Address Threats

### META

- **M**itigate - make threat harder to exploit.
  - Threat: spoofing via brute forcing.
  - Mitigation: require longer passwords, lock account after 3 attempts, use biometrics.
- **E**liminate - remove the feature that was exposed to the threat.
  - Remove user accounts.
- **T**ransfer - let another party assume the risk.
  - Use "login with Facebook" for user accounts.
- **A**ccept - when no other choice.

