# 3.miniRSA

> Let's decrypt this: ciphertext? Something seems a bit small.

---

## Solution:

- The challenge provided a file named `ciphertext` which contained an encrypted message.  
- Upon opening it in a text editor, I noticed that the file included three labeled values — **N**, **C**, and **e**.  
- I wasn’t initially sure what encryption scheme this was, so I searched online using these terms together and discovered that it matched the structure of an **RSA cipher**.  
- Interestingly, I later noticed that one of the hints in the challenge also mentioned RSA, confirming my assumption.  
- While reading about RSA, I found that the **public exponent (e)** value in this challenge was very small compared to normal RSA configurations (commonly `e = 65537`).  
- I suspected that this weakness might make it easier to decrypt directly.  
- I then found an online RSA decryption tool where I could enter **N**, **C**, and **e**.  
- Using [dcode.fr’s RSA Cipher Tool](https://www.dcode.fr/rsa-cipher), I input these values and successfully obtained the flag.

---

## Flag:

```
picoCTF{n33d_a_lArg3r_e_ccaa7776}
```

---

## Concepts learnt:

- **RSA encryption basics** — uses a modulus (**N**), public exponent (**e**), and ciphertext (**C**).  
- **Small exponent vulnerability** — very small values of `e` can make RSA encryption less secure, especially when padding or message randomization is absent.  
- Identifying encryption schemes based on the structure and naming of parameters.  

---

## Notes:

- Always open unknown files in a text editor first; structured numerical data often indicates encryption or encoding.  
- When facing a challenge with RSA, check the **key size** and **e value** first — small `e` can be a major clue.  
- Online tools like dcode.fr are very useful for simple or weak RSA challenges where the math isn’t obfuscated.  

---

## Resources:

- [RSA Cipher Decoder – dcode.fr](https://www.dcode.fr/rsa-cipher)  
- Basic RSA references and documentation to understand key generation and vulnerabilities.  

---

## Incorrect Tangents:

- Initially considered if the ciphertext contained hidden data like steganography or encoding — but the labeled numerical values clearly pointed toward cryptography instead.  
- Spent a few minutes checking for appended data or patterns before realizing the RSA structure made that unnecessary.  

