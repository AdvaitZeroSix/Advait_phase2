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

