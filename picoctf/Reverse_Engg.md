# 1.GDB baby step 1

> Can you figure out what is in the eax register at the end of the main function?  
> Put your answer in the picoCTF flag format: `picoCTF{n}` where `n` is the contents of the `eax` register in decimal.  
> (If the answer was 0x11 your flag would be picoCTF{17}).  
> *Disassemble this.*

---

## Solution:

- I was given a binary file named `debugger0_a`. The challenge asked to find the value stored in the **eax register** at the end of the main function.  
- To start, I opened the terminal in Ubuntu and disassembled the binary using the following command:

  ```
  objdump -d -Mintel debugger0_a
  ```

- The output displayed the assembly instructions of the program. The most important part was the **main** function, which looked like this:

  ```
  0000000000001129 <main>:
      1129: f3 0f 1e fa             endbr64
      112d: 55                      push   rbp
      112e: 48 89 e5                mov    rbp,rsp
      1131: 89 7d fc                mov    DWORD PTR [rbp-0x4],edi
      1134: 48 89 75 f0             mov    QWORD PTR [rbp-0x10],rsi
      1138: b8 42 63 08 00          mov    eax,0x86342
      113d: 5d                      pop    rbp
      113e: c3                      ret
      113f: 90                      nop
  ```
  
- We focus on the key line:  
 ```
  mov eax, 0x86342
  ```
  This instruction directly loads the **hexadecimal value 0x86342** into the `eax` register.  

- Since the `mov` instruction explicitly sets `eax` to **0x86342** and nothing changes it afterward, the final value of `eax` at the end of `main` is **0x86342**.

- Converting this hexadecimal value to decimal:
  ```
  0x86342 = 549698
  ```

- Therefore, the final flag in the required format is:

---

## Flag:

```
picoCTF{549698}
```

---

## Concepts learnt:

- How to use **objdump** to disassemble a binary and read assembly instructions in Intel syntax.  
- How to identify the **main function** and follow register assignments step by step.  
- On the x86 architecture, the return value of a function is stored in the **eax register** — so analyzing its final value directly gives the answer.  
- Quick conversion between hexadecimal and decimal for CTF flags.

---

## Notes:

- Most of the basic assembly language codes can be predicted just by reading the code instead of actaually needing to run the file

---

## Resources:

- `objdump` manual (for `-d -Mintel` disassembly). 
- Basic x86 assembly guides to understand function prologues and epilogues.

---

## Incorrect Tangents:

- Initially thought I might need to run the binary and observe behavior dynamically, but the constant `mov eax, 0x86342` made it clear that static analysis was enough.
# 2. ARMssembly 1

> For what argument does this program print `win` with variables 58, 2 and 3?  
> File: chall_1.S  
> Flag format: picoCTF{XXXXXXXX} → (hex, lowercase, no 0x, and 32 bits. ex. 5614267 would be picoCTF{0055aabb})

---

## Solution:

- Upon entering the challenge, we are provided with a file named `chall_1.S`. Opening it in Visual Studio Code, I observed that it was written in **assembly language**.  
- Since I had already watched a tutorial on how to approach such problems, I began by reading through the file systematically.  
- As the description mentioned, the goal was to reach the **win condition**, which was labeled `.LC0`.

```
.LC0:
    .string "You win!"
    .align 3
```

- This meant that to print “You win!”, the program had to satisfy a specific condition in the code logic.  
- I then examined the `func` function and started adding comments to understand what each line did.

```
func:
	sub	sp, sp, #32
	str	w0, [sp, 12]
	mov	w0, 58
	str	w0, [sp, 16] ; 58 at sp,16
	mov	w0, 2
	str	w0, [sp, 20] ; 2 at sp,20
	mov	w0, 3
	str	w0, [sp, 24]; 3 at sp,24
	ldr	w0, [sp, 20]
	ldr	w1, [sp, 16]
	lsl	w0, w1, w0 ; left shift 58 by 2 gives 232
	str	w0, [sp, 28] ; stored at sp,28
	ldr	w1, [sp, 28]
	ldr	w0, [sp, 24]
	sdiv	w0, w1, w0 ; floor divide 232/3 = 77
	str	w0, [sp, 28]
	ldr	w1, [sp, 28]
	ldr	w0, [sp, 12]
	sub	w0, w1, w0 ; subtracts input number from 77
	str	w0, [sp, 28]
	ldr	w0, [sp, 28]
	add	sp, sp, 32
	ret
```

- From this, it was clear that:
  - 58 was shifted left by 2 → giving 232  
  - 232 was divided by 3 → giving 77  
  - Then the user input was subtracted from 77  

- In the **main** function, the result from `func` was compared with 0.  
  If it equaled 0, it would reach `.LC0` and print **“You win!”**, otherwise `.LC1` which printed **“You Lose :(”**.  

- Hence, to reach the win condition, the value of the user input should make the function return 0, i.e. when the input = 77.  
- Finally, converting 77 into hexadecimal gave `0x4D`.  
  Following the challenge format, the final flag was:

---

## Flag:

```
picoCTF{0000004d}
```

---

## Concepts learnt:

- Understood how **ARM assembly** performs logical operations such as `lsl` (logical shift left) and `sdiv` (signed divide).  
- Learned how functions pass and return values using registers (`w0`, `w1` etc.) and how the **stack pointer (sp)** is used to store temporary values.  

---

## Notes:

- Reading ARM assembly takes time, but commenting alongside each line made it much easier to understand.  

---

## Resources:

- https://www.youtube.com/watch?v=1d-6Hv1c39c   
- https://www.google.com  

---

## Incorrect Tangents:

- No incorrect tangents.

