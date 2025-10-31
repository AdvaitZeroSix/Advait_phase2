# 2.Custom Encryption

> Can you get sense of this code file and write the function that will decode the given encrypted file content. Find the encrypted file here flag_info and code file might be good to analyze and get the flag.

---

## Solution:

-Open the given links, which download a text file and a python script.
Import the python file into vscode.
-The python script is an encryption sequence that appears to be using a and b to ENCRYPT a sequence.
-Since I have my own a and b, I need to somehow construct a decryption script and use that with the a, b and cipher to get the flag.
-On reading the script, I saw multiple functions. A generator function took 3 variables and performed a simple arithmatic operation.
-To get the decrypt script I need to reverse all the opeartions.
-It also uses 2 values p and g for the encryption, 97 and 31 respectively.
-To reverse, we need to reverse each operation.so i wrote a code to find the initial argument(i.e. the flag) by giving the code the final data

```
a = 90
b = 26
cipher is: [61578, 109472, 437888, 6842, 0, 20526, 129998, 526834, 478940, 287364, 0, 567886, 143682, 34210, 465256, 0, 150524, 588412, 6842, 424204, 164208, 184734, 41052, 41052, 116314, 41052, 177892, 348942, 218944, 335258, 177892, 47894, 82104, 116314]
```

my code was:
```
a=90
b=26
p=97
g=31
cipher=[61578, 109472, 437888, 6842, 0, 20526, 129998, 526834, 478940, 287364, 0, 567886, 143682, 34210, 465256, 0, 150524, 588412, 6842, 424204, 164208, 184734, 41052, 41052, 116314, 41052, 177892, 348942, 218944, 335258, 177892, 47894, 82104, 116314]

def generator(g, x, p):
    return pow(g, x) % p

def dynamic_xor_encrypt(plaintext, text_key):
    k=""
    cipher_text = ""
    key_length = len(text_key)
    for i, char in enumerate(plaintext[::-1]):
        key_char = text_key[i % key_length]
        k+=key_char
        encrypted_char = chr(ord(char) ^ ord(key_char))
        cipher_text += encrypted_char
    print(k)
    return cipher_text

v = generator(g, b, p) 
key = generator(v, a, p)
print(key)

decipher = "" 
for i in cipher:
    decipher += chr((i // 311 // key))

print(dynamic_xor_encrypt(decipher,"aedurtu"))
```

in dynamic_xor_encrypt i first entered picoCTF{ then it gave me the recurring jumbled "aedurtu"
then i used that to get the final flag
the final flag was picoCTF{custom_d2cr0pt6d_49fbee5b}

---

## Flag:

```
picoCTF{custom_d2cr0pt6d_49fbee5b}
```

---

## Concepts Learnt:

- Reversing encryption steps to decrypt data
- Understanding the flow of XOR encryption and decryption
- Using modular arithmetic in encryption schemes

---

## Notes:

- Reading through the code and understanding each function helped in reversing the encryption.
- Recognizing the repeating XOR key “aedurtu” was important to get the correct final flag.

---

## Resources:

- Python documentation

---

## Incorrect Tangents:

- Tried to decode directly without reversing each step
- Tried multiple random XOR keys before realizing the pattern “aedurtu” repeated

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
<img width="1220" height="378" alt="image" src="https://github.com/user-attachments/assets/151cd381-8cda-4f06-bf35-c168fa32697a" />

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

