# 1.Buffer Overflow 0

> Let's start off simple, can you overflow the correct buffer? The program is available here. You can view source here.
> Additional details will be available after launching your challenge instance.

---

## Solution:

- I read the challenge hints provided:
  1) How can you trigger the flag to print?  
  2) If you try to do the math by hand, maybe try and add a few more characters. Sometimes there are things you aren't expecting.  
  3) Run `man gets` and read the BUGS section. How many characters can the program really read?

- I inspected the given `vuln.c` source. The program uses `gets()` (an unsafe function) to read input into a fixed-size buffer. The BUGS section of `man gets` explicitly warns that `gets()` does not do bounds checking and can read arbitrarily many characters, making it vulnerable to buffer overflow.

- I launched the challenge instance and connected to it with netcat:
  ```
  nc saturn.picoctf.net 58367
  ```

- After connecting, the service prompted for input. Knowing the task was to overflow the correct buffer, I sent an oversized input (a long string of characters). For this challenge a single long payload was sufficient; for example:
  ```
  input:hdbfjsdbfjbdsjfbdsjfbadlsfbdjsbfdasbfladsbkfhjabsdkfhbkashjbfkahbsdkfjhbaksdhfbakshdbfjkadhsbfjhadsbfkhdsbfkahdsbkfhabdskfhjbadskhfbkadsjhbfkadhjsbfkjadhsbfadhjsbfkdhjsbfkadhjsbfkhadsbfkhadsbfkhjadsbfjkhdsbfkjhdsbfkjhdsabfkjhabds
  ```

- Submitting this long input triggered the program’s vulnerable behavior and caused it to print the flag.

---

## Flag:

```
picoCTF{ov3rfl0ws_ar3nt_that_bad_9f2364bc}
```

---

## Concepts learnt:

- `gets()` is inherently unsafe because it performs no bounds checking and can read arbitrarily many characters into a fixed-size buffer. The `man gets` BUGS section warns against its use.  
- Buffer overflows can be exploited simply by supplying more input than a buffer can hold; sometimes the intended challenge is just recognizing `gets()` and overflowing the buffer rather than performing advanced return-oriented programming.  
- When a service asks for input over the network, reproduce the behavior locally or connect with `nc` to test payloads interactively.

---

## Notes:

- Always check source code for unsafe input functions
- If you need to determine exact overflow lengths, iterative fuzzing with increasing payload sizes helps

---

## Resources:

- `man gets` (see BUGS section)  

---

## Incorrect Tangents:

- No alternate tangents


# 2.Format String 0

> Can you help the picky customers find their favorite burger?  
> Connect using `nc mimas.picoctf.net 61846` and figure out which burger to serve each customer.

---

## Solution:

- I was given a binary (`format-string-0`) and its source code (`format-string-0.c`), but I didn’t really need to use them directly since the challenge could be solved through the provided network connection.

- I connected to the challenge using the given **netcat** command:
  ```
  nc mimas.picoctf.net 61846
  ```

- After connecting, I was greeted with:
  ```
  Welcome to our newly-opened burger place Pico 'n Patty! Can you help the picky customers find their favorite burger?
  Here comes the first customer Patrick who wants a giant bite.
  Please choose from the following burgers: Breakf@st_Burger, Gr%114d_Cheese, Bac0n_D3luxe
  ```

### Level 1 — Patrick

- The clue here is in the line: **“wants a giant bite”**.  
  The menu includes a burger name with `%114d`, which in a format string means “print 114 spaces” — essentially a **large** (giant) print width.  

- So, I chose:
  ```
  Gr%114d_Cheese
  ```

- The format specifier expanded the output and triggered the next level because it printed more than twice the buffer size (as checked in the code with `if (count > 2 * BUFSIZE)`).

- The output confirmed success:
  ```
  Good job! Patrick is happy! Now can you serve the second customer?
  ```

---

### Level 2 — SpongeBob

- The next prompt said:
  ```
  Sponge Bob wants something outrageous that would break the shop (better be served quick before the shop owner kicks you out!)
  Please choose from the following burgers: Pe%to_Portobello, $outhwest_Burger, Cla%sic_Che%s%steak
  ```

- The hints were clear:
  - “break the shop”  
  - “before the shop owner kicks you out”

  Both hinted towards **causing a segmentation fault** — which, looking at the source code, triggers the `sigsegv_handler()` that prints the flag when a segmentation fault occurs.

- The burger `Cla%sic_Che%s%steak` contains `%s`, which tries to interpret random stack data as a string, likely causing a segmentation fault.  

- I selected:
  ```
  Cla%sic_Che%s%steak
  ```

- As expected, the program crashed, and the flag was printed!

---

## Flag:

```
picoCTF{7h3_cu570m3r_15_n3v3r_SEGFAULT_dc0f36c4}
```

---

## Concepts learnt:

- **Format string vulnerabilities** — how `%d`, `%s`, `%x`, etc., can change how user input is processed.  
- The `%d` format specifier with a large width (`%114d`) can overflow output buffers or change control flow when printed.  
- `%s` attempts to read a string from a memory address, which can cause segmentation faults if that address is invalid.   
- Using hints from the challenge text is key — “giant bite” → `%d`, “break the shop” → `%s`.

---

## Notes:

- In format string CTFs, always look at the hints hidden in the text or variable names — they often describe the exploit type.  
- Understanding the source code helps confirm how input is handled (in this case, `printf(choice1)` and `printf(choice2)` without format safety).  
- Causing a segmentation fault intentionally can sometimes be the *intended* path to the flag if there’s a signal handler defined.

---

## Resources:

- C `printf()` format specifiers documentation.   

---

## Incorrect Tangents:

- I initially thought maybe the first burger name was random text and didn’t notice the `%114d` significance.  
- I also briefly tried “safe” inputs, but the challenge required triggering risky behavior (buffer overflow and segfault).

# 3.Clutter Overflow

> Clutter, clutter everywhere and not a byte to use. `nc mars.picoctf.net 31890`

---

## Solution:

- The challenge provided a remote service (`nc mars.picoctf.net 31890`) and a C source file. I downloaded and read the C source to understand the expected input and behavior.  
- The source used `gets()` to read input into a buffer named `clutter`, which is a classic insecure function and a strong indicator of a buffer overflow challenge. The program printed an error saying that `code` should equal `0xdeadbeef`, so the goal was to overwrite the `code` variable with that value.  
- I ran the program locally (and/or connected to the remote service) to observe behavior and to craft an exploit payload. To overflow `code`, I needed to determine the exact number of bytes from the start of `clutter` to the `code` variable on the stack. The `clutter` buffer size in the source was 256 bytes.  
- I first sent 256 garbage characters followed by a marker (`a` `z`) to see if it would overwrite `code`. That did not produce an overflow, so I incremented the payload length in steps (I increased by 4 bytes each time) until I observed overwriting behavior. The buffer began overflowing at **264** bytes.  
- With the correct offset known, I needed to write the 32-bit value `0xdeadbeef` into `code`. Because it uses little-endian, I supplied the bytes in little-endian order: `\xef\xbe\xad\de`.  


---

## Flag:

picoCTF{c0ntr0ll3d_clutt3r_1n_my_buff3r}

---

## Concepts learnt:

- `gets()` is unsafe.  
- Determining the correct overflow offset can be done iteratively (increase payload size until overwrite occurs).
- Endianness matters: to set a 32-bit value in memory to `0xdeadbeef` on a little-endian system you must send the bytes in reverse order: `\xef\xbe\xad\xde`.  
- Some shells/tools require escape interpretation (`echo -e`) to send non-printable bytes over netcat.

---

## Notes:

- An other way could be to use program scripts to overflow the clutter variable automatically and place the value deadbeef into code
- I did not understand why I got no output when I entered the string directly and why it only worked when I used echo -e

---

## Resources:

- `man gets` 
-  little endian converter

---

## Incorrect Tangents:

- Attempting to provide printable ASCII for the `0xdeadbeef` value instead of raw bytes — endianness and raw byte injection are required for this task.  
