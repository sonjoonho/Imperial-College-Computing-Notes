# 14 - JavaScript

## JavaScript Transformations

### Minification

The source code of scripts is sent over the internet. The length of a script affects latency of page load times and consumes network bandwidth. Hence, scripts can be **minified** by removing comments, spaces, newlines, shortening identifiers, sharing constants, etc.

### Inspection

The source code of scripts is available for inspection. The algorithm may be *intellectual property*. If the script is malicious, it may contain pointers to attacker-controlled *assets* (domains, IPs, keys). Scripts can be **obfuscated** to hinder analysis, reverse-engineering, and detection.

## Obfuscation

Obfuscated code should be hard or impossible to deobfuscate, but preserve the behaviour of the script nor make it too slow. 

### Joining a String Array

![image-20200216214215281](14 - JavaScript.assets/image-20200216214215281.png)

### String Manipulation

![image-20200216214231080](14 - JavaScript.assets/image-20200216214231080.png)

### String Encoding

![image-20200216214240866](14 - JavaScript.assets/image-20200216214240866.png)

### Identifier Mangling

![image-20200216214250734](14 - JavaScript.assets/image-20200216214250734.png)

### Encryption

![image-20200216214307480](14 - JavaScript.assets/image-20200216214307480.png)

