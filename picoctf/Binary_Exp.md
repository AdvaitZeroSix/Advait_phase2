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

