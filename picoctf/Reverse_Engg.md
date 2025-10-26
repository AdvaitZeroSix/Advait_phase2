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

- https://www.youtube.com/watch?v=gh2RXE9BIN8  
- https://www.youtube.com/watch?v=1d-6Hv1c39c  
- https://www.youtube.com/playlist?list=PLMB3ddm5Yvh3gf_iev78YP5EPzkA3nPdL  
- https://www.google.com  

---

## Incorrect Tangents:


