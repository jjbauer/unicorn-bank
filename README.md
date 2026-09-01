# 🦄 Unicorn Bank

A tiny single-file web app that teaches a 6-year-old about saving and interest.
Built for one kid, run by a parent. No accounts, no backend, no real money moves —
just a fun ritual around watching savings grow.

## Live app

- **URL:** https://jjbauer.github.io/unicorn-bank/
- **Add to her device:** open the URL in Safari → Share → *Add to Home Screen*. It
  launches full-screen like a real app.
- **Grown-up PIN:** `1234` (change the `const PIN` line near the top of the script in
  `index.html`).

## How it's hosted (important)

- This repo (`jjbauer/unicorn-bank`, **public**) is the **source of truth** for the
  live site. It contains only `index.html` (+ this README). GitHub Pages serves it
  from `main` / root.
- It lives in its **own** repo on purpose: the app was built inside the private
  `jjbauer/Claude_Code` monorepo, and Pages must **never** be enabled there (it would
  expose private files). A backup of the app also sits on the
  `jjbauer/kids-banking-interest-app` branch of that monorepo.

## Making a change

Edit `index.html`, then push to this repo's `main`:

```bash
# from a clone of THIS repo (not the monorepo)
git add index.html
git commit -m "describe the change"
git push origin main
```

Pages redeploys automatically (~1 min). Note: GitHub's CDN + browser caching can serve
the old version for a minute or two — hard-refresh (or add `?x=1` to the URL) to bust it.

## How interest works

- **5% per week**, paid on **Fridays**, compounding.
- Interest doesn't auto-apply. Each Friday a rainbow **Collect** button appears and she
  taps it to claim it (the fun moment). It stacks and waits if she misses a week.
- **New money must be saved a full week before it earns.** The app tracks `principal`
  (money that's been saved through a Friday) separately from the total `balance`. A
  deposit made this week skips the upcoming Friday and starts earning the one after —
  so a deposit never earns interest the same week it's added.
- The interest "clock" is the last Friday paid through (`state.lastInterest`). It only
  advances when she collects.

## Grown-up tools (behind the PIN)

- **📅 Add past deposit** — back-fill a real deposit with the Friday she actually saved
  it (1–4 Fridays ago). Logs it dated, marks it already-saved, and owes interest for
  every Friday since.
- **✏️ Set balance** — force the total to a number; treats the whole amount as
  already-saved (eligible to earn).
- **🗑️ Edit history** — delete a row; also undoes that transaction's effect on the
  balance.
- **⏩ Make a Friday's interest ready** — rewind the clock one Friday so a Friday's
  interest becomes collectable now.
- **Reset everything** — clean slate ($0, no history).

## Known limits (by design)

- **Per-device.** The balance/history live in the browser's `localStorage`, so each
  device is its own separate piggy bank. Use the **same device** as "the bank" (her
  phone/iPad). Interest back-pays correctly even after time away, so that's fine.
- Not real security — the PIN is in the page source. It's a speed bump to keep little
  fingers out of the grown-up tools, nothing more.

## State shape (for reference)

`localStorage["kidbank_v1"]`:
```
{ balance, principal, lastInterest, history: [{label, emoji, amount, when}], v: 3 }
```
All money amounts are integer **cents**. `RATE = 0.05`, interest paid weekly on Fridays.
