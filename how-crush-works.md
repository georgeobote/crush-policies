# How Crush Works

_Last updated: 2026-01-04_

Think of this page as the friendlier, product-focused tour of Crush. We'll walk through what students see, why the daily drops feel intentional, and how we keep the community safe, without dumping raw technical docs on you. Need the fine print? Check the [Terms of Service](https://georgeobote.github.io/crush-policies/terms-of-service), [Privacy Policy](https://georgeobote.github.io/crush-policies/privacy-policy), or the engineering-heavy [data inventory](https://georgeobote.github.io/crush-policies/data-inventory).

## 1. Eligibility & Onboarding

1. **Download & sign in:** Grab the app, confirm your phone with SMS, and you’re in. Behind the scenes we light up a Firebase session and register your device for notifications.
2. **`.edu` verification:** Because it’s a college-only community, we ask for a school email. Tap “Send code,” grab the OTP from your inbox, and that unlocks discovery. No `.edu`, no Crush.
3. **Build your profile:** Add three or more photos (needed to keep Crush Drops active), a bio, major, grad year, connection goals, who you're looking to meet, athletics/clubs, Greek life, deal breakers, pet preference/has pets, religion, and lifestyle choices (smoking, drinking, going out). You also pick campus/state/nationwide scopes and radius sliders so the matcher knows where to look.
4. **School vibes:** Once the `.edu` is confirmed, we map it to your school so colors, mascots, and drop windows all feel tailored to that campus and time zone.

## 2. Profile signals & themes

- Everyone starts with our “Neutral earth” palette, warm, calm, and easy on the eyes.
- Verified students can toggle to their school pride theme anytime from Settings → Appearance. It instantly swaps the UI to your campus colors.
- Compatibility uses the signals you add: majors/clubs, Greek life, location scopes, connection goals, religion, pets, age + grad year, smoking/drinking/going-out style, and optional deal breakers (same school only, Greek-life only/skip, athlete-only/skip).
- We keep track of school coordinates so your countdowns, drop windows, and notifications stay in the right time zone even if you travel.

## 3. Crush Drop Windows

- **Three drop slots:** Morning (10–11 AM), afternoon (3–4 PM), and evening (8–9 PM) in your school’s time zone. Each day we hash the time zone + date + window to pick one shared minute so the reveal feels like a “BeReal” moment for everyone nearby.
- **Stay photo-ready:** Drops only run if you keep the `Crush Drop` toggle on, maintain at least three approved photos, and have verified SMS + `.edu` with gender + “looking for” set. Fall below that, and we pause things until you’re ready again.
- **Response timers:** Match windows stay open until local midnight with ready/reminder/expire notifications. If neither side answers in time, we roll forward and log it so fairness and analytics stay intact.
- **Transparent history:** Every window stores whether it was upcoming, pairing, match ready, no match, or skipped so you can track how the day went and we can keep drops fair.

## 4. Pairing Intelligence

1. **Campus-first always:** We start with your campus, then expand to state and nationwide if needed. Distance penalties for off-campus/out-of-state matches help keep long-distance intentional, and we occasionally sample a wider radius to avoid stale pools.
2. **Balanced scoring:** Light recent activity + profile freshness, distance, shared majors/clubs, Greek life, age + grad year alignment, connection goals, religion, pets, and lifestyle signals (smoking, drinking, going out) feed the behind-the-scenes score.
3. **Guardrails before scoring:** We enforce mutual gender preferences, deal breakers (same school only, Greek-life only/skip, athlete-only/skip), block lists, same-day repeat-pair blocks, and existing-match checks before we ever surface a candidate.
4. **Shared reveal moments:** As soon as a match is confirmed, both people get slotted into the same upcoming drop window so the spotlight reveal happens together.

## 5. Spotlight Reveal & Decisions

- When your drop minute arrives, the app fades everything else out and spotlights your match with their lead photo, school flair, shared interests, and lifestyle callouts.
- Tap **Meet** to lean in or **Pass** to skip. Both people must tap Meet before chat unlocks, classic double opt-in. You can always open the Daily Drops list to revisit active windows before the response timer expires.
- We quietly log decisions so we can improve pairing over time and keep window stats accurate.

## 6. Chat, Safety & Reporting

- **Chat unlocks after both tap Meet.** Messages run through a callable Cloud Function with reasonable rate limits so nobody gets spammed.
- **Block/report anywhere.** From any card or chat, you can block (mutual) or file a report. Reports go straight to our Trust & Safety queue for review.
- **Photo moderation:** Every upload runs through Google Cloud Vision SafeSearch. If something looks sketchy, it never appears in the app and we keep a moderation log.
- **Easy exits:** Deleting your account from Settings triggers the full cleanup, Auth profile, Storage photos, Firestore data, the works.

## 7. Notifications & Controls

- We send push notifications for new drops, matches, messages, and "Hey, your spotlight is waiting" reminders. Toggle match/message/drop alerts in-app or at the OS level.
- Email is only for `.edu` verification and critical safety/account updates. No marketing blasts unless you explicitly opt in.
- Inline nudges (like “Upload 3+ photos to resume drops”) keep you in the loop without guesswork.

## 8. Data & Transparency

- We only store what’s spelled out in the [Privacy Policy](https://georgeobote.github.io/crush-policies/privacy-policy) and guard it with Firebase security rules (public profiles are only visible to signed-in students).
- Curious about the nitty-gritty? The [data inventory](https://georgeobote.github.io/crush-policies/data-inventory) lists every collection, field (including lifestyle + compatibility signals), and retention promise.
- Questions, audits, or press inquiries: reach us via the support flow in the app or email `hello@crushso.com`.

---

Want something else covered here? Open an issue or PR, we keep this page in lockstep with releases so the GitHub Pages version stays fresh without spilling proprietary details.
