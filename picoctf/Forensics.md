# 1.Trivial Flag Transfer Protocol

> Figure out how they moved the flag.

---

## Solution:

- I downloaded the provided `pcapng` file and opened it in **Wireshark** to inspect the traffic. Early in the capture, I noticed files related to the transfer: `instructions`, `plan`, and three image files (`picture1.bmp`, `picture2.bmp`, `picture3.bmp`).  
- I exported the transferred objects using **File → Export Objects → TFTP** in Wireshark and saved the files locally.  
- The `instructions` and `plan` files contained readable text that looked encoded. I ran these through a ROT13 checker and decoded them. The decoded messages said:

  > "TFTP doesn't encrypt our traffic so we must disguise our flag transfer. Figure out a way to hide the flag and I will check back for the plan."

  and

  > "I used the program and hid it with - due diligence. Check out the photos."

- The hint (`due diligence`) suggested a passphrase for a steganography tool. I examined the three BMP images next. To make analysis easier I installed `binwalk` (useful for extracting embedded data) and also learned the images likely contained hidden data using **steghide**.
- I gathered it hides files in BMPs/WAVs and can encrypt the hidden data with a passphrase. The mention of “- due diligence” strongly suggested the passphrase `due diligence` or a variant.
- I tried `steghide` on each image, testing passphrase variants: `"due diligence"`, `due diligence` (no quotes), `duediligence`, and `DUE DILIGENCE`. Picture2 failed, but **picture3.bmp** contained a hidden file.
- Using `steghide --extract -sf picture3.bmp -p DUEDILIGENCE` (and supplying the passphrase), I extracted a text file. Reading it with `cat` revealed the flag.

---

## Flag:

```
picoCTF{h1dd3n_1n_pLa1n_51GHT_18375919}
```

---

## Concepts learnt:

- How to inspect network captures in **Wireshark** and export transferred objects (TFTP).  
- ROT13 is a simple substitution cipher often used to hide hints — try common ciphers on suspicious text.  
- **Steghide** can hide files in BMP/WAV containers and requires a passphrase; clues in the capture can point to that passphrase.  
- Use tools like `binwalk` and `steghide` together: `binwalk` helps find embedded data, `steghide` extracts it when you know (or guess) the passphrase.  
- Always try small passphrase variants (quotes, no quotes, joined words, caps) when hints include punctuation or dashes.

---

## Notes:

- When Wireshark shows TFTP transfers, use **Export Objects → TFTP** to quickly recover files the attacker moved.  
- Common stego passphrases in CTFs are short phrases from hints — try literal, no-space, and uppercase/lowercase variants.  
- If `steghide` fails repeatedly, `binwalk` or `strings` can still reveal embedded payloads or other clues.

---

## Resources:

- Wireshark docs — exporting objects (TFTP).  
- steghide manual and tutorials.  
- binwalk documentation and basic steganography writeups.

---

## Incorrect Tangents:

- I initially focused on `binwalk` for thorough binary carving, but the capture’s text hints made the steghide/passphrase route faster and cleaner.

