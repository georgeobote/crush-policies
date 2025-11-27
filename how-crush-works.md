# How Crush Works

_Last updated: 2025-11-27_

Think of this page as the friendlier, product-focused tour of Crush. We’ll walk through what students see, why the daily drops feel intentional, and how we keep the community safe—without dumping raw technical docs on you. Need the fine print? Check the [Terms of Service](../legal/terms-of-service.md), [Privacy Policy](../legal/privacy-policy.md), or the engineering-heavy [data inventory](../privacy/data-inventory.md).

## 1. Eligibility & Onboarding

1. **Download & sign in:** Grab the app, confirm your phone with SMS, and you’re in. Behind the scenes we light up a Firebase session and register your device for notifications.
2. **`.edu` verification:** Because it’s a college-only community, we ask for a school email. Tap “Send code,” grab the OTP from your inbox, and that unlocks discovery. No `.edu`, no Crush.
3. **Build your profile:** Add three or more photos, a short bio, your major, graduation year, and anything else that shows off your campus life (clubs, athletics, Greek-life, etc.). You also pick how far you want to meet people—campus, statewide, or nationwide.
4. **School vibes:** Once the `.edu` is confirmed, we match it to your school so colors, mascots, and local drop windows all feel tailored.

## 2. Personalization & Themes

- Everyone starts with our “Neutral earth” palette—warm, calm, and easy on the eyes.
- Verified students can toggle to their school pride theme anytime from Settings → Appearance. It instantly swaps the UI to your campus colors.
- We keep track of school coordinates so your countdowns, drop windows, and notifications stay in the right time zone even if you travel.

## 3. Crush Drop Windows

- **Three drop slots:** Morning (10–11 AM), afternoon (3–4 PM), and evening (8–9 PM) in your school’s time zone. We pre-assign your exact minute so the countdown feels legit and predictable.
- **Stay photo-ready:** Drops only run if you keep the `Crush Drop` toggle on and maintain at least three approved photos. Fall below that, and we pause things until you’re ready again.
- **Transparent history:** Every window stores whether it was pending, revealed, matched, or skipped so you can see how the week went and we can make sure everyone gets a fair shot.

## 4. Matching Intelligence

1. **Campus-first always:** We try your classmates first. If sparks aren’t flying, we slowly widen to nearby schools, then statewide, and finally nationwide—with heavier filters so long-distance matches really feel worth it.
2. **Balanced scoring:** Major, clubs, Greek life, profile completeness, recency, and distance all feed the behind-the-scenes score. None of the secret sauce is public, but the goal is to surface active, well-rounded students.
3. **Shared reveal moments:** As soon as a match is confirmed, both people get slotted into the same upcoming drop window so the spotlight reveal happens together.

## 5. Spotlight Reveal & Decisions

- When your drop minute arrives, the app fades everything else out and spotlights the match with their lead photo, school flair, and shared interests.
- Tap **Interested** to lean in or **Skip** to pass. Both people must tap Interested before chat unlocks—classic double opt-in.
- We quietly log decisions so the matcher learns your preferences and we can make sure window stats stay accurate.

## 6. Chat, Safety & Reporting

- **Chat unlocks after the double yes.** Messages live in an encrypted Firestore thread with reasonable rate limits so nobody gets spammed.
- **Block/report anywhere.** From any card or chat, you can block (mutual) or file a report. Reports go straight to our Trust & Safety queue for review.
- **Photo moderation:** Every upload runs through Google Cloud Vision SafeSearch. If something looks sketchy, it never appears in the app and we keep a moderation log.
- **Easy exits:** Deleting your account from Settings triggers the full cleanup—Auth profile, Storage photos, Firestore data—the works.

## 7. Notifications & Controls

- We send push notifications for new drops, matches, messages, and “Hey, your spotlight is waiting” reminders. Toggle them in-app or at the OS level.
- Email is only for `.edu` verification and critical safety/account updates. No marketing blasts unless you explicitly opt in.
- Inline nudges (like “Upload 3+ photos to resume drops”) keep you in the loop without guesswork.

## 8. Data & Transparency

- We only store what’s spelled out in the [Privacy Policy](../legal/privacy-policy.md) and guard it with Firebase security rules.
- Curious about the nitty-gritty? The [data inventory](../privacy/data-inventory.md) lists every collection, field, and retention promise.
- Questions, audits, or press inquiries: reach us via the support flow in the app or email `hello@crushso.com`.

---

Want something else covered here? Open an issue or PR—we keep this page in lockstep with releases so the GitHub Pages version stays fresh without spilling proprietary details.
