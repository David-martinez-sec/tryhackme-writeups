# TryHackMe — JavaScript Room: Notes & Takeaways
**Author:** David Martinez  
**Room:** JavaScript (TryHackMe)  
**Date:** 2025-11-06

---

## Overview
This room introduces practical, security-minded JavaScript fundamentals and how JS fits into real-world web applications. It combines hands-on code, attack/defense scenarios, and build/obfuscation workflows to show both how features work and how they can be abused. These notes are written as a GitHub-ready reference and reflection.

---

## What I covered (high-level)
- JavaScript basics: variables, types, operators, functions.
- Linking JS to HTML (`<script>`, external files, `defer`, `async`).
- Browser interactivity: `prompt()`, `alert()`, `confirm()`, DOM manipulation.
- Control flow: `if/else`, `switch`, loops (`for`, `while`), and short-circuit evaluation.
- Attacker abuse scenarios: bypassing client-side controls, manipulating prompts, XSS-like patterns.
- Minification and obfuscation: minify/uglify, obfuscate and reversing basics.
- Best practices & hardening to reduce attack surface.

---

## JavaScript in HTML
**Inline script**
```html
<script>
  console.log('inline script running');
</script>
```

**External script**
```html
<script src="app.js" defer></script>
```
- `defer` executes after HTML parsing; `async` executes as soon as the script downloads (use `async` for independent scripts).

---

## Basic syntax reminders
- Declarations: `let`, `const`, `var` (prefer `let` / `const` in modern code).
- Function styles:
```js
function add(a, b) { return a + b; }
const add = (a, b) => a + b;
```
- Primitive types: `string`, `number`, `boolean`, `null`, `undefined`, `object`, `symbol`.

---

## Browser interactive features (use carefully)
- `prompt()` — gets string input from user
- `confirm()` — gets boolean (OK/Cancel)
- `alert()` — modal notification (poor UX; used for demos)

**Security note:** Inputs from these are untrusted. Attackers can spoof or script interactions—always validate and sanitize server-side.

---

## Control flow & bypass considerations
- Client-side checks are **UX** improvements and convenience — they can be bypassed.
- Bypass techniques:
  - Disable JavaScript
  - Modify DOM with DevTools (change `disabled` attributes, hidden inputs)
  - Intercept and alter network requests with a proxy (e.g., Burp)
- **Rule:** Server-side validation is authoritative.

---

## Minify & Obfuscate
- **Minification:** removes comments/whitespace and shortens identifiers to reduce size (tools: `terser`, `uglify-js`).
- **Obfuscation:** transforms code to make it harder to read (renames vars, flattens control flow). It deters casual inspection but does not secure secrets.
- Example `terser` command:
```bash
npx terser app.js -c -m -o app.min.js
```

---

## Security-focused best practices
- **Never trust client input.** Validate and sanitize on the server.
- **Do not store secrets** or privileged API keys in client-side JS.
- **Use CSP (Content Security Policy)** to restrict script sources and reduce XSS risk.
- **Escape user content** before inserting into the DOM (prefer `textContent` over `innerHTML`).
- **Set secure cookie flags:** `HttpOnly`, `Secure`, `SameSite`.
- **Avoid `eval()` and `new Function()`** — they enable code injection.
- **Use frameworks** (React, Vue, Svelte) that encourage safer rendering patterns, but still practice escaping and validation.
- **Log and rate-limit** suspicious client behaviors server-side.

---

## Useful examples & snippets

**Safe DOM insertion**
```js
// Unsafe
element.innerHTML = `<div>${userInput}</div>`;

// Safer
const div = document.createElement('div');
div.textContent = userInput;
element.appendChild(div);
```

**Client-side (UX) vs Server-side (authoritative) validation**
Client:
```js
if (!email.includes('@')) {
  showError('Please use a valid email');
}
```
Server (Node/Express pseudo):
```js
if (!/^\S+@\S+\.\S+$/.test(req.body.email)) {
  return res.status(400).json({ error: 'invalid email' });
}
```

---

## Lab practicals — what I ran / experimented with
- Built a simple HTML page and linked `app.js`.
- Tested `prompt()` and `confirm()` and observed spoofing via DevTools.
- Minified `app.js` using `terser` to compare size and readability.
- Tried a basic JS obfuscator and practiced reversing to see the limits of obfuscation.
- Tampered with DOM elements in DevTools to confirm client-side control bypasses.

---

## Lessons learned
- JavaScript is powerful for UX but **inherently untrusted** for security.
- Minification/obfuscation improve performance/deterrence; they do not replace secure design.
- Small APIs like `prompt()` can become vectors for social engineering if server-side logic is weak.

---

## Next steps & learning plan
- Build small front-end projects to solidify DOM/event handling and browser APIs.
- Learn one modern front-end framework (React, Vue, or Svelte) for safer rendering patterns.
- Build a small CRUD app with a secure backend (Node + Express + SQLite/Postgres) to practice client-server boundaries and server-side validation.
- Add CSP to projects and practice escaping user content in real examples.

---

## Personal reflection
Working through this TryHackMe JavaScript room increased my interest in JavaScript and front-end development because it combined creative UX-building with tangible security implications. I enjoyed building interactive features and then immediately testing how they could be abused, which highlighted the dual role of a front-end developer: create delightful experiences and defend against real-world threats. This experience motivated me to keep learning frameworks, improve my UI skills, and build secure, polished web apps.
