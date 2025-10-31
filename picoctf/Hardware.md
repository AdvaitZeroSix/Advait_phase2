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

