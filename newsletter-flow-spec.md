# Newsletter — pre-submit hint + auto-dismissing post-submit banner

## What & why

Two product moments around the newsletter form:

1. **Pre-submit**: a quiet helper line below the form. Sets expectation about double-opt-in before the user clicks (GDPR Art. 13 informed-consent friendly, removes "what just happened" anxiety).
2. **Post-submit**: a light-blue info banner replaces the form with the full disclosure. Holds for 5s, fades out, then the section returns to its default state so the slot doesn't sit empty.

Reference behavior on the About Us repo: commit `a332588` (and the few commits before it).

## HTML

Inside the newsletter section, alongside the existing `<form>`, add **two siblings**:

```html
<!-- Pre-submit hint: one quiet line. Centered. -->
<p class="news__hint">Bestätigung per E-Mail · Ihre Daten bleiben bei uns.</p>

<!-- Post-submit confirmation: blue info banner. Hidden until JS reveals it. -->
<div class="news__success" role="status" aria-live="polite" hidden>
  <svg class="news__success-icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.8" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
    <circle cx="12" cy="12" r="10"/>
    <path d="M12 8v4M12 16h.01"/>
  </svg>
  <p class="news__success-text">
    Wir senden Ihnen eine E-Mail mit einem Freischalt-Link für den Erhalt unseres Newsletters. Die Abmeldung ist jederzeit möglich.
  </p>
</div>
```

If you're replacing an older "Ihre Daten bleiben bei uns" lock note, remove it — the new hint absorbs it.

## CSS

```css
/* Pre-submit hint — quiet centered line below the form */
.news__hint{
  margin: 0;
  font-size: 12px;
  font-weight: 500;
  color: #7a7a7a;
  letter-spacing: 0.02em;
  text-align: center;
}

/* Post-submit banner. The [hidden] rule MUST come before the display rule
   so it wins over `display: flex` (otherwise the banner is visible on load). */
.news__success[hidden]{ display: none; }
.news__success{
  display: flex;
  align-items: flex-start;
  gap: 12px;
  margin: 0 auto;
  padding: 14px 18px;
  background: #E7F3FB;
  border: 1px solid #C9E2F3;
  border-radius: 4px;     /* or your --r-m token */
  color: #1B6FAE;
  text-align: left;
  max-width: 520px;
  opacity: 1;
  transform: translateY(0);
  transition: opacity 500ms ease, transform 500ms cubic-bezier(.2,.7,.2,1);
}
.news__success.is-dismissing{
  opacity: 0;
  transform: translateY(-4px);
}
.news__success-icon{ width: 20px; height: 20px; flex: 0 0 auto; margin-top: 1px; color: #2C8DD2; }
.news__success-text{ margin: 0; font-size: 14px; line-height: 1.5; color: inherit; }

/* Subscribed state — hide form + hint while banner is up */
.news__inner.is-subscribed .news__form,
.news__inner.is-subscribed .news__hint{ display: none; }
```

## JS

Add this script after the form's DOM. Plain vanilla, no deps:

```html
<script>
  (function(){
    var form = document.querySelector('.news__form');
    if (!form) return;
    var inner = form.closest('.news__inner');
    var success = inner && inner.querySelector('.news__success');
    if (!inner || !success) return;

    form.addEventListener('submit', function(e){
      e.preventDefault();
      var input = form.querySelector('input[type="email"]');
      if (!input || !input.checkValidity()){
        input && input.reportValidity();
        return;
      }
      inner.classList.add('is-subscribed');
      success.hidden = false;
      success.setAttribute('tabindex', '-1');
      success.focus();   // screen-reader announcement

      // After 5s: fade for 500ms, then restore default state.
      setTimeout(function(){
        success.classList.add('is-dismissing');
        setTimeout(function(){
          success.hidden = true;
          success.classList.remove('is-dismissing');
          inner.classList.remove('is-subscribed');
          input.value = '';
        }, 600);
      }, 5000);
    });
  })();
</script>
```

## Gotchas (real ones we hit)

1. **`display: flex` overrides `hidden`**. The browser's `[hidden]` attribute uses `display: none`, but any explicit CSS `display` rule beats it. The `.news__success[hidden] { display: none; }` rule above is required — without it the banner is visible on initial page load.

2. **Don't use `transitionend` for cleanup.** It's unreliable when the element was just made visible from a `display: none` state — the initial style isn't always committed before the class flip, and the transition silently skips. Use a fixed `setTimeout(..., 600)` instead (matches the 500ms transition + 100ms buffer).

3. **Class name on the wrapper must be `.news__inner`** (or whatever container holds both the form and the banner). The `is-subscribed` state is applied there, not on the form itself.

4. **Input value clearing matters.** If you don't clear `input.value`, the next visitor on the same session sees the previous user's email. Especially important on shared kiosks / multi-user devices.

## Test plan

- [ ] Initial load: form visible, hint line below, banner not rendered (verify `[hidden]` works).
- [ ] Submit invalid email: native browser validation fires; no state change.
- [ ] Submit valid email: form + hint hide instantly, banner appears with focus moved to it.
- [ ] Wait 5 seconds: banner starts fading (opacity 1 → 0 over 500ms, slight upward drift).
- [ ] After ~5.6s: banner removed from layout (`display: none`), form + hint reappear, input is empty.
- [ ] Re-submit: cycle repeats.
