---
name: landing-page-workflow
description: Implementation-specific notes for the index.html codebase in this project (the Unlimited Abundance / Lux Vacations Rental landing page) — CSS/JS gotchas already hit once, and this repo's exact GitHub/Vercel endpoints. Use this whenever editing index.html in this project. For brand facts (pricing, contact info, destinations, visual identity) see the global lux-vacations-rental-brand skill; for the general GitHub Desktop → Vercel deploy mechanics see the global deploy-static-site-vercel skill — this skill only covers what's unique to this specific file.
---

# This project's implementation notes

This is a single-file static site (`index.html`, vanilla HTML/CSS/JS, no build step). Two other skills cover the rest of what you need:

- **`lux-vacations-rental-brand`** (global) — business facts, pricing, contact info, destinations, colors/fonts. Load it for *what to say and how it should look*.
- **`deploy-static-site-vercel`** (global) — the generic GitHub Desktop → Vercel walkthrough. Load it for *how to get a change live* in general.

What's left here is only the stuff specific to *this file's code* that isn't generic and isn't brand knowledge — mistakes already made once in this codebase, worth not repeating.

## CSS specificity trap (already hit once)

The question labels in the lead-capture form use `.field > label` (direct-child combinator), **not** `.field label`. If this ever gets changed back to a descendant selector, it will re-match the `.option` labels nested inside `.options` (they're also `<label>` elements sitting inside a `.field`), silently collapsing their `display:flex` layout and making the multi-step form's "selected" state nearly invisible. This exact bug happened once during development — before touching the form's label CSS, double check this selector is still scoped correctly.

## Multi-step form UX (intentional, not a bug)

- Each radio group is a `.options` div. Selecting one option adds `.answered` to that div, which hides the sibling `.option` labels and reveals a `<button type="button" class="change-answer">Change answer</button>` (must be the last child of `.options`) to undo the pick. This collapse-on-select behavior was an explicit design request — don't "fix" it back to showing all options at once.
- The selected state is a navy-filled card with a gold checkmark (`.option.selected`). A lighter tint was tried first and reported as nearly invisible against the surrounding cream background — keep the bold navy/gold treatment.
- New radio groups need three pieces to work with the existing generic JS (which wires up every `.option input[type=radio]` and every `.change-answer` button by class selector, not by ID, so no new JS is needed for new groups): the `.option` labels themselves, the trailing `.change-answer` button, and a `required` attribute on at least one radio in the group if the step should block "Continue" until answered.
- There's no backend. Submission builds a `mailto:` link from all answers and opens it, then shows a success panel. If the user wants real lead capture (a database, spreadsheet, or CRM), that's a decision to surface to them, not something to assume — see the brand skill's note on not inventing business facts, same principle applies to infrastructure decisions.

## This repo's exact endpoints

- GitHub: `https://github.com/luxvacationsrental-design/Landing-Page-Lux-Vacatiosn-Rental` (the "Vacatiosn" typo in the name is live infrastructure now — not worth the churn of renaming and re-linking Vercel)
- Vercel: Team **"Lux CODE"** (Hobby plan), project auto-deploys on push to `main`
- Live URL: **https://landing-page-lux-vacatiosn-rental.vercel.app**
- This machine has no Node/npm/Homebrew and this session has no stored GitHub credentials/TTY — pushes always go through GitHub Desktop, per the deploy-static-site-vercel skill. Don't attempt `git push` from a shell here expecting it to succeed.

**After any edit to this file, always end the response with the push reminder** (open GitHub Desktop → Commit to main → Push origin → Vercel auto-deploys) — full step-by-step is in `deploy-static-site-vercel`, but don't skip giving it just because it's "already covered elsewhere"; the user asked to be reminded every single time.
