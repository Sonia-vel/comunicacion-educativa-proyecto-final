# 001 — Animate the quiz feedback entrance (Ramona's hint and "¡Correcto!")

- **Status**: DONE
- **Commit**: 389b1fa
- **Severity**: MEDIUM
- **Category**: Missed opportunities (teleporting state)
- **Estimated scope**: 1 file (`index.html`), ~15 lines changed

## Problem

In the "Reto rápido" quiz (Actividad 3), choosing an answer swaps the feedback
under the question instantly. When the answer is wrong, a whole Ramona hint
bubble (44px avatar + speech bubble) appears in one frame and shoves every
question below it down. When it is right, the "¡Correcto!" line pops in the
same way. This is the page's main interactive moment, and it's the moment
when a child gets something wrong. Right now it feels like the page glitched,
not like Ramona answering.

The feedback is rendered by `showFeedback` in `index.html:807-816`:

```js
/* index.html:807 — current */
function showFeedback(name) {
  const i = Number(name.slice(4)) - 1, item = QUIZ[i], fb = document.getElementById("fb_" + name);
  if (!fb) return;
  if (data[name] === undefined || data[name] === "") { fb.textContent = ""; fb.className = "feedback"; return; }
  const ok = Number(data[name]) === item.a;
  fb.className = "feedback " + (ok ? "ok" : "no");
  if (ok) { fb.textContent = "¡Correcto! " + item.why; return; }
  fb.innerHTML = '<div class="ramona mini"><span class="av"></span><div class="bubble"><b class="who">Pista de Ramona</b><p>' + item.hint + ' Inténtalo de nuevo.</p></div></div>';
  ramonaAvatar(fb.querySelector(".av"));
}
```

It is called in three places:

```js
/* index.html:830 — on every answer change */
    if (k.startsWith("a3_q")) showFeedback(k);
/* index.html:834 — on page load, to restore saved answers */
QUIZ.forEach((_, i) => showFeedback("a3_q" + (i + 1)));
/* index.html:895 — after "Sí, borrar" (empties every feedback) */
  QUIZ.forEach((_, i) => showFeedback("a3_q" + (i + 1)));
```

Three details shape the fix:

1. The correct-answer branch writes a **bare text node** (`fb.textContent = …`).
   A CSS rule on `.feedback > *` can't reach a text node, so it has to be
   wrapped in an element. That element must be block-level, because
   `transform` doesn't apply to inline elements like `<span>`.
2. Saved answers are restored on page load (line 834). The entrance must
   **not** play then, or every returning student sees five bubbles animate in
   at once.
3. The reduced-motion rule at `index.html:53` kills **all** transitions with
   `!important`:

   ```css
   /* index.html:53 — current */
   @media (prefers-reduced-motion: reduce) { html { scroll-behavior: auto; } * { transition: none !important; animation: none !important; } }
   ```

   Reduced-motion users should still get a short opacity fade (gentler, not
   zero), so the `transition: none !important` part has to go. A grep shows
   there are **no other `transition` declarations** in the file, so removing
   it changes nothing else today.

## Target

```css
/* :root gets one easing token */
--ease-out: cubic-bezier(0.23, 1, 0.32, 1);

/* only after the quiz has finished its initial render */
#quiz.ready .feedback > * {
  transition: opacity 200ms var(--ease-out), transform 200ms var(--ease-out);
  transform-origin: 22px 22px; /* center of the 44px mini avatar: the bubble grows out of Ramona */
  @starting-style { opacity: 0; transform: translateY(4px) scale(0.96); }
}
@media (prefers-reduced-motion: reduce) {
  #quiz.ready .feedback > * {
    transition: opacity 150ms var(--ease-out);
    @starting-style { opacity: 0; transform: none; }
  }
}
```

Behaviour:

- Each wrong answer rewrites `fb.innerHTML`, which creates a new element, so the
  entrance replays. That's intentional: a different wrong answer brings a different hint.
- There is no exit animation. Feedback is replaced or cleared instantly.
- In browsers without `@starting-style` (Chrome < 117, Safari < 17.5,
  Firefox < 129), the feedback just appears instantly, as it does today. No fallback is needed.

## Repo conventions to follow

- All CSS lives in the single `<style>` block in `index.html:11-245`, grouped
  by section with a short Spanish comment header (`/* Quiz */`,
  `/* Ramona: … */`). Design tokens live in `:root` at `index.html:13-35`,
  e.g. `--r: 14px;`. Add the easing token there.
- The JS builds markup with string concatenation and `innerHTML` (see
  line 814). Follow that idiom; don't switch to `createElement`.
- `<p>` already has `margin: 0` globally (`index.html:67`), so wrapping text in
  `<p>` adds no spacing.
- Comments in this file are in Spanish. Keep any comment you add in Spanish.

## Steps

1. **Add the easing token.** In `:root` (`index.html:34`), after `--r: 14px;`, add:

   ```css
     --ease-out: cubic-bezier(0.23, 1, 0.32, 1);
   ```

   Don't add it to the dark-mode blocks. It isn't a color.

2. **Let reduced motion keep fades.** Replace line 53 exactly with:

   ```css
   @media (prefers-reduced-motion: reduce) { html { scroll-behavior: auto; } * { animation: none !important; } }
   ```

   (Only `transition: none !important;` is removed.)

3. **Add the entrance CSS.** Directly after line 177
   (`.feedback { font-size: .9rem; min-height: 1.4em; }`, inside the `/* Quiz */`
   group), insert:

   ```css
   /* La retroalimentación entra solo después de la carga inicial */
   #quiz.ready .feedback > * {
     transition: opacity 200ms var(--ease-out), transform 200ms var(--ease-out);
     transform-origin: 22px 22px;
     @starting-style { opacity: 0; transform: translateY(4px) scale(0.96); }
   }
   @media (prefers-reduced-motion: reduce) {
     #quiz.ready .feedback > * {
       transition: opacity 150ms var(--ease-out);
       @starting-style { opacity: 0; transform: none; }
     }
   }
   ```

4. **Wrap the correct-answer text in an element.** In `showFeedback`, replace line 813:

   ```js
     if (ok) { fb.textContent = "¡Correcto! " + item.why; return; }
   ```

   with:

   ```js
     if (ok) { fb.innerHTML = '<p>¡Correcto! ' + item.why + '</p>'; return; }
   ```

   (`item.why` comes from the hard-coded `QUIZ` array, not from user input, so
   `innerHTML` is safe here, the same as `item.hint` on line 814.)

5. **Turn the entrance on after the initial render.** Directly after line 834
   (`QUIZ.forEach((_, i) => showFeedback("a3_q" + (i + 1)));`), add:

   ```js
   quiz.classList.add("ready");
   ```

   `quiz` is already defined at line 798 (`const quiz = document.getElementById("quiz");`).
   Elements that already exist when `.ready` is added do not use
   `@starting-style`, so restored feedback won't animate.

## Boundaries

- Only edit `index.html`, and only the lines named in the steps.
- Do NOT change the `.ramona` / `.ramona.mini` styles, the quiz markup built at
  lines 799-806, `ramonaAvatar`, or the save/restore logic.
- Do NOT add an exit animation, keyframes, a JS animation library, or any dependency.
- Do NOT animate `height`, `margin` or other layout properties to smooth the
  push-down of the questions below. Animate only `opacity` and `transform`.
- If any quoted "current" code doesn't match the file (drift since 389b1fa),
  STOP and report instead of improvising.

## Verification

- **Mechanical**: there is no build step. Run
  `grep -n "ease-out\|#quiz.ready\|classList.add(\"ready\")\|<p>¡Correcto" index.html`
  and confirm four kinds of match: the token in `:root`, the two CSS rules,
  the `<p>¡Correcto!` line, and `quiz.classList.add("ready")`. Open `index.html`
  in Chrome or Safari and confirm there are no console errors.
- **Feel check**: open the page, scroll to Actividad 3 › "Reto rápido":
  - Choose a wrong answer. Ramona's hint should fade in and grow slightly out
    of her avatar (top-left), not out of the bubble's center, and settle within about 0.2s.
  - Choose a different wrong answer. The hint should replay its entrance.
  - Choose the right answer. "¡Correcto! …" should rise 4px and fade in.
  - Rapidly click between options. Nothing should get stuck half-transparent.
  - Reload the page with answers saved. Restored feedback should be **already
    there**, with nothing animating on load.
  - Click "Borrar todo" › "Sí, borrar". Feedback should clear instantly with no errors.
  - DevTools › Animations panel at 10% speed: confirm the hint never starts
    from `scale(0)`, only from 0.96, and the avatar side stays anchored.
  - DevTools › Rendering › emulate `prefers-reduced-motion: reduce`: the
    feedback should fade (150ms) with no movement or scale.
  - Can't be judged from code: the questions below still jump down instantly
    when a hint appears. On a tablet, check whether the fade softens that
    enough. If the jump still feels harsh, report it rather than fixing it in this plan.
- **Done when**: all five steps are applied, the grep matches as above, and
  every feel-check item passes.
