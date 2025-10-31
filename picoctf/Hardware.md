# 1.IQ Test

> Let your input x = 30478191278.  
> Wrap your answer with `nite{ }` for the flag.  
> Example: if x = 34359738368 gives (y0, ..., y11) = 010000000011, the flag would be `nite{010000000011}`.

---

## Solution:

- The challenge provided an integer input `x = 30478191278` and hinted at logic operations producing a 12-bit output `(y0, ..., y11)` that forms the flag.  
- Downloading the provided files revealed that the challenge involved **logic gate simulations** — inputs processed through multiple Boolean operations.  
- Because the logic gates only handle **binary inputs**, I first converted `x` to its **36-bit binary** form.  
- Adding a leading 0 (for alignment with 36 expected values) produced the following 36-bit representation:
  ```
  011100011000101001000100101010101110
  ```
- I then manually solved each logic gate as described in the challenge files, propagating the 1s and 0s through the logic circuit.  
- After completing all the logic gate computations, the resulting binary sequence for `(y0, ..., y11)` was:
  ```
  100010011000
  ```
- Wrapping that output in the required flag format gives the final answer.

---

## Flag:

```
nite{100010011000}
```

---

## Concepts learnt:

- Converting decimal numbers to fixed-width binary (here, 36 bits) to match expected input size for logic circuits.  
- Understanding and manually simulating **logic gates** (AND, OR, XOR, NOT) to derive output bits.  
- How binary sequences can encode challenge results or flags directly in logic-based puzzles.  

---

## Notes:

- Always confirm bit length when converting numbers for logic or hardware challenges — missing a bit can shift all gate results.  
- Writing down intermediate outputs after each logic stage helps catch propagation mistakes early.  
- Logic-based challenges often have small outputs (e.g., 8–12 bits) that translate directly to a flag format.  

---

## Resources:

- Online binary converters.  

---

## Incorrect Tangents:

- No incorrect tangents

# 2.I like Logic

> I like logic and I like files, apparently, they have something in common. What should my next step be?

---

## Solution:

- The challenge provided a folder containing a `.sal` file.  
- A quick search revealed that `.sal` files are used by **Saleae Logic Analyzer** software — which matched the description’s mention of “logic files.”  
- Opening the file in Saleae Logic showed multiple channels, and I noticed that the **third channel** contained binary data (1s and 0s).  
- I first tried exporting the raw data directly, but it didn’t contain any readable or useful content.  
- Next, I searched for an **ASCII analyzer** tool compatible with Saleae logic captures. After decoding the data through this analyzer, I started to see readable ASCII text forming words.  
- I exported the analyzed data into a `.txt` file and examined it carefully. Looking through the text, I found a string enclosed in curly braces `{}` — the typical format for CTF flags.  
- The data within those braces turned out to be the challenge flag.

---

## Flag:

```
FCSC{b1dee4eeadf6c4e60aeb142b0b486344e64b12b40d1046de95c89ba5e23a9925}
```

---

## Concepts learnt:

- `.sal` files are Saleae Logic Analyzer project files that can contain captured signal data from hardware.  
- Logic analyzers can export binary data that often represents **ASCII-encoded communication** (UART, SPI, etc.).  
- Understanding how to use protocol or ASCII analyzers is essential to extract meaningful data from raw signal captures.  

---

## Notes:

- When encountering unknown file types, start by searching their extension — it often leads directly to the required tool.  
- In Saleae or similar tools, identify which channel carries meaningful data (the one showing bit transitions).  
- ASCII analyzers are powerful for extracting text-based communication from digital signal captures.  
- Always check for patterns like `{}` that could indicate flag structures in decoded data.

---

## Resources:

- Saleae Logic Analyzer software and documentation.

---

## Incorrect Tangents:

- Initially tried using the raw exported binary without analysis — it was unreadable. The correct approach was to apply an **ASCII protocol analyzer** to interpret the binary stream.  

# 3.Bare Metal Alchemist

> my friend recommended me this anime but i think i've heard a wrong name.

---

## Solution:

- The attached file was an ELF binary (`firmware.elf`). I first tried to execute it directly:
  ```
  ./firmware.elf
  ./firmware.elf: cannot execute binary file: Exec format error
  ```
  That error suggested the binary was built for a different architecture or OS.
- I ran `objdump -d firmware.elf` to inspect it, but `objdump` couldn't disassemble it and reported an unknown architecture:
  ```
  firmware.elf:     file format elf32-little
  objdump: can't disassemble for architecture UNKNOWN!
  ```
- I searched for a way to detect the correct architecture. The file metadata showed:
  ```
  Class: ELF32
  Machine: Atmel AVR 8-bit microcontroller
  ```
  So this was AVR firmware (not a standard x86/x86_64 Linux binary).
- I tried `avr-objdump -d firmware.elf` and experimented with tools like Ghidra, but they either failed or required more setup than I wanted to do at the time. Instead of digging deeper into AVR reverse-engineering, I tried a simpler brute-force approach on the file contents.
- I wrote a small Python script that XOR'd every byte of the file with every possible 1-byte key (1..255), then searched the decoded output for a flag-like pattern (`CTF{...}`). The script looked like this:
  ```python
  import re

  FILENAME = "firmware.elf"

  with open(FILENAME, "rb") as f:
      data = f.read()

  for key in range(1, 256):
      decoded = bytes([b ^ key for b in data])
      m = re.search(rb'CTF\{[^}]{1,200}\}', decoded)
      if m:
          print("Found flag with key 0x{:02x}:".format(key))
          print(m.group(0).decode())
          break
  else:
      print("No flag found with single-byte XOR.")
  ```
- Running this script revealed the flag in the decoded output after trying the correct single-byte XOR key.

---

## Flag:

```
CTF{Th1s_1s_som3_s1mpl3_4rdu1no_f1rmw4re}
```

---

## Concepts learnt:
 
- When reverse-engineering firmware, sometimes simple heuristic brute-force (single-byte XOR) can quickly reveal readable strings if the file was lightly obfuscated.

---

## Notes:

- If you want to do deeper firmware analysis later, set up Ghidra or radare2 with AVR support and use `avr-objdump` to get instruction listings.  
- The XOR brute-force was a quick pragmatic approach — it worked here because the flag was present in the file after a fixed single-byte XOR transformation.

---

## Resources:

- `file`, `objdump`, and `avr-objdump` for architecture detection and disassembly.   
- Python `re` for searching decoded outputs.

---

## Incorrect Tangents:

- Trying to run the ELF as a native Linux binary — that produces `Exec format error` when architectures differ.  
- Spending a long time configuring Ghidra/R2 before trying quick heuristics; simple tests can pay off early.
