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

# 2.Tunn3l V1s10n

> We found this file. Recover the flag.  
> Hint: Weird that it won't display right...

---

## Solution:

- **Files given:** `tunn3l_v1s10n` (no extension).

- **Initial recon:**
  - I checked the file metadata with `exiftool` and confirmed it's a BMP image (1134×306, 24-bit).  
  - I inspected the file with `binwalk` which found some odd signatures but no obvious embedded files to extract.  
  - I opened the file in a hex editor (hexed.it) and verified the BMP header and raw bytes — there were no obvious appended files or magic headers for embedded archives.

- **Approaches tried (in order):**
  1. **Data appending / file structure exploits** — checked for appended archives or extra magic but nothing useful was found.  
  2. **Masking and filtering** — considered imagefilter based stego but deferred deeper learning until other options were exhausted.  
  3. **Steganography tools** — tried common tools (`steghide`, `zsteg` where applicable) but they did not reveal a payload.  
  4. **Dimension / pixel-interpretation manipulation (the winning method)** — based on the hint “won't display right”, I suspected the image's width/height or row stride might be wrong. I returned to the hex editor to experiment with header fields rather than extracting hidden files.

- **Dimension manipulation details:**  
  - Changing the **width** field repeatedly corrupted the file, so I switched to experimenting with the **height** field.  
  - I iteratively modified the BMP height value and opened the image each time to see how it rendered. I tried values like `400, 450, 500, ...` and found corruption starting around `850` pixels. I then tested heights starting from `810` upward.  
  - At a height of **830 pixels**, the image rendered correctly and revealed the hidden flag printed inside the image.

- **Flag:**  
```
  picoCTF{qu1t3_a_v13w_2020}
```
---

## Concepts learnt:

- File type metadata (via `exiftool`) can be correct even when an image's internal dimension fields are intentionally wrong.  
- Not all stego is about embedded files; sometimes the data is present but misinterpreted due to incorrect header fields (dimension/stride). Changing image dimensions can re-interpret the pixel stream correctly.  
- Iterative, small changes (start coarse then refine) are useful when brute-forcing header fields — here, stepping height upwards and testing revealed the correct value (830).

---

## Notes / practical tips:

- When an image “won’t display right”, try reinterpreting dimensions (swap width/height) or tweak the header height/width values by small amounts.  
- Always keep a backup of the original file before editing headers. Use a hex editor that preserves file structure and lets you revert easily.  
- If width changes immediately corrupt the file, try adjusting height or padding instead — BMP row padding can be subtle.  

---

## Resources:

- `exiftool`, `binwalk`, hex editors (e.g., hexed.it) and Image viewers (default OS viewer) are handy for these tasks.  
- Steganography writeups discussing dimension/stride manipulation and BMP internals.

---

## Incorrect Tangents:

- Relying solely on appended-file checks (binwalk) — this was a useful first step but would not have found the pixel-interpretation trick.

# 3.m00nwalk

> Decode this message from the moon.

---

## Solution:

- **Files given:** A link that downloads an **audio file**.


  - The challenge name *m00nwalk* and the description *“Decode this message from the moon”* hint toward the **moon landing**.
  - The provided hint — *“How did pictures from the moon landing get sent back to Earth?”* — directly pointed toward researching the transmission method.
  - Researching the technology used during the Apollo missions revealed that video transmissions from the moon used **SSTV (Slow-Scan Television)** — a method of encoding images as audio tones.
  - I searched online for **SSTV decoding tools** and learned about how SSTV transmits image data through audio frequencies.
  - I uploaded the provided audio file to an online SSTV decoder.
  - The decoded output revealed an **image file** containing the flag.
  
- **Flag:**  
  ```
  picoCTF{beep_boop_im_in_space}
  ```

---

## Concepts learnt:

- **SSTV (Slow-Scan Television):** A method used to transmit static images over radio using sound frequencies.  
- How **audio files** can contain **visual data** encoded as tone variations.  
- Basic understanding of how to identify SSTV-encoded signals and decode them using online tools.

---

## Notes:

- When an audio challenge references space or transmission, **think about SSTV or other radio-based encoding** methods.  
- Always start by researching key terms from the challenge (like *moon transmission* in this case).  
- Free online SSTV decoders can save time if you don’t want to set up tools locally.

---

## Resources:

- [SSTV Decoder Online](https://sstv-decoder.mathieurenaud.fr/)  
- Google searches about Apollo 11 video transmission and SSTV

---

## Incorrect Tangents:

- Initially, it was tempting to check for hidden data like **steganography in the audio**, but the “moon transmission” hint clearly pointed toward SSTV encoding.

