# 2.SSTI1

> I made a cool website where you can announce whatever you want! Try it out!  
> Additional details will be available after launching your challenge instance.

---

## Solution:

- On opening the challenge page, there was a simple web form with a single text input and a **Go**/submit button. Whatever was entered got printed back to the page — and then you had to go back to enter more text. This behavior suggested that user input was being rendered server-side into a template rather than being safely escaped on output.

- My initial goal was to confirm whether this was a **reflected server-side template rendering** (an SSTI). The typical first test is to submit an innocuous expression that will evaluate if the server uses a template engine.

- **Recon / detection:**  
  I tried different payloads to detect which template engine was in use, for example:
  - `{% 7*7 %}`
  - `{{5*5}}`
  - `{{7*7}}`

  Submitting `{{5*5}}` (and similar `{{...}}` expressions) produced evaluated results instead of literal text, and the syntax/behavior matched Jinja2-style evaluation — so I concluded the site uses **Jinja2**.

- **Exploitation:**  
  Since the target used Jinja2, I escalated by trying to access Python builtins via the `request` object exposed in the template context. The first successful payload I used to list files in the app directory was:

  ```
  {{request.application.__globals__.__builtins__.__import__('os').popen('ls').read()}}
  ```

  This returned the following files in the application directory:
  - `__pycache__`
  - `app.py`
  - `flag`
  - `requirements.txt`

  With the flag file confirmed present, I then read it with a similar payload:

  ```
  {{ request.application.__globals__.__builtins__.__import__('os').popen('cat flag').read() }}
  ```

  This returned the flag text.

---

## Flag:

```
picoCTF{s4rv3r_s1d3_t3mp14t3_1nj3ct10n5_4r3_c001_bcf73b04}
```

---

## Concepts learnt:

- How to detect Jinja2-style SSTI using simple expression tests (`{{5*5}}`).  
- How to use template context objects (like `request`) to access Python builtins and `os` via `__import__`.  
- Typical pattern for file enumeration (`os.popen('ls').read()`) and file retrieval (`os.popen('cat flag').read()`) in CTF-style Jinja2 SSTI scenarios.  
- Importance of incremental testing: start with safe probes, then escalate to file reads once the environment is better understood.

---

## Notes:

- First list files (ls), then read the flag file (e.g., cat flag).
- Only try this on CTFs or systems you are allowed to test.
- Test quickly with {{7*7}} — if it shows 49 the site evaluates templates (likely Jinja2).
- Start with harmless probes (math/string echoes), then enumerate objects & types, then escalate to listing files, and only then read the flag. This reduces noise and avoids accidental breakage.
- 
---

## Resources:

https://onsecurity.io/article/server-side-template-injection-with-jinja2/
https://www.yeswehack.com/learn-bug-bounty/server-side-template-injection-exploitation

---

## Incorrect Tangents:

Alternate tangent 1: Tried checking cookies before searching up what SSTI1.

# 3. Cookies

> Who doesn't love cookies? Try to figure out the best one.
> [http://mercury.picoctf.net:64944/](http://mercury.picoctf.net:64944/)
> Flag format: picoCTF{XXXXXXXX}

## Solution:

* I opened the challenge URL in a browser and saw a form that asked me to enter different cookie names.
* I first tried common cookie names like `chocolate chip` and `oatmeal raisin`. Each returned the message: "That is a cookie! not very special though..."
* Because this is a *Web Exploitation* challenge and the name of the challenge is **Cookies**, I suspected the browser cookies (the HTTP cookies) might be relevant.
* I opened the browser DevTools → **Application** → **Cookies** and observed the cookie(s) set by the site. The default cookie value was `-1`.
* I interacted with the site by entering cookie names and watched the cookie value change; it took small integer values like `1, 2, 3, ...` depending on input.
* I inferred that a particular numeric value stored in the cookie would cause the site to reveal the flag. Instead of continuing to try names, I switched strategy and tried entering numbers directly into the input to see if the cookie would take the numeric value.
* I manually tested numbers up to 20. When I entered the number `18`, the site returned the flag and the challenge was completed.

## Flag:

```
picoCTF{3v3ry1_l0v3s_c00k135_cc9110ba}
```

## Concepts learnt:

* Inspecting and manipulating browser cookies via DevTools (Application → Cookies) can reveal hidden state the server relies on.
* For web challenges that change behavior based on stored values, it can be faster to set numeric or edge-case values directly rather than guessing names.
* Brute forcing small integer ranges by hand is sometimes sufficient; for larger ranges, automation would be preferable.

## Notes:

* A faster approach (if needed) would be to automate requests that set the cookie value and check the response for the flag string. For example, a simple Python `requests` loop that sets the cookie and looks for the flag in the response body.

## Resources:

* Browser DevTools — Application tab (Cookies)

## Incorrect Tangents:

* Initially tried many cookie *names* (chocolate chip, oatmeal, etc.) assuming the magic value would be a name; this was unnecessary once it became clear the cookie stored an integer index.

---

