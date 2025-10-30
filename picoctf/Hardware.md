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

