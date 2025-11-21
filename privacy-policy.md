# Privacy Policy

_Last updated: 2025-11-21_

Crush (“we”, “us”, “our”) provides a college-only dating application that relies on SMS authentication, `.edu` email verification, swipe-based discovery, Crush Drop scheduling, in-app chat, and Firebase-hosted infrastructure. This Privacy Policy explains what data we collect, how we use and share it, and the choices and rights available to you. It complements the technical inventory found in `docs/privacy/data-inventory.md`; if anything conflicts, this policy controls.

## Information We Collect

We collect the following categories of data when you use the app:

1. **Account & Identity Data:** Display name, phone number, `.edu` email, school, graduation year, gender, age, Greek-life affiliation, majors, clubs, athletics, looking-for preferences, preferred drop scope, and Firebase Auth identifiers.
2. **Verification Data:** SMS verification codes, `.edu` email OTP attempts (hashed), school domain mapping, opt-in status for Crush Drop, and verification timestamps.
3. **Profile Content (UGC):** Bios, prompts, photos uploaded to Firebase Storage, and any optional fields you share such as interests or Greek organization details.
4. **Discovery & Location Data:** Scope selection (campus/state/nationwide), distance sliders, GPS coordinates (rounded to ~110 m), state and school-level fallback details, and presence timestamps.
5. **Engagement & Messaging Data:** Swipe decisions, Crush Drop window history, matches, chat messages (stored in Firestore), like/match/message activity feed events, push tokens, notification preferences, and messaging rate-limit counters.
6. **Safety & Support Data:** Block lists, reports, support tickets, photo moderation outcomes, account deletion logs, and SafeSearch audit entries.
7. **Device & Usage Data:** Firebase Analytics event identifiers, app version, OS type, and push token metadata (including last-seen timestamps).

We do not knowingly collect information from individuals under 18; using the app requires a qualifying `.edu` address and college enrollment.

## How We Use Your Information

We process data for the following purposes:

- **Service delivery:** Authenticate accounts, verify college enrollment, build swipe feeds, run Crush Drop scheduling, surface the spotlight modal, and deliver in-app messaging.
- **Personalization & discovery:** Apply school themes, render profile cards, respect gender preferences, distance filters, and location-based scopes.
- **Intelligent matching:** Score potential Crush Drop partners using profile completeness, shared majors/clubs, Greek-life participation, recent activity/presence, distance, and historical opt-in behavior so the campus-first algorithm can widen responsibly.
- **Safety & integrity:** Enforce eligibility, detect abusive content, honor block lists, and moderate photos via Google Cloud Vision SafeSearch.
- **Communications:** Send SMS codes, `.edu` verification emails (via SendGrid), push notifications for matches/messages, and support responses.
- **Analytics & diagnostics:** Monitor aggregate usage, message rate limits, and Crush Drop performance to keep the product stable and fair.
- **Compliance:** Maintain data-retention records, user deletion logs, and evidence of opt-ins/consents for audit purposes.

## How We Share Information

We do not sell personal data. We share it only with:

- **Service providers:** Firebase (Auth, Firestore, Storage, Analytics, Cloud Messaging, Cloud Functions), Google Cloud Vision, and SendGrid for `.edu` emails.
- **Other users:** Profiles, matches, chat messages, and presence indicators are visible only to participants consistent with Firestore security rules.
- **Legal & safety recipients:** We may disclose information to comply with law, enforce our Terms, or protect the rights and safety of users.

All processors are bound by confidentiality and data protection agreements. We remain responsible for their handling of your data.

## Retention

- SMS and `.edu` verification attempts are purged after the TTLs described in `functions/index.js`.
- Swipes older than 90 days, inactive matches/messages after 30 days, and stale push tokens after 60 days are deleted automatically.
- Reports, support tickets, and presence logs currently follow manual retention schedules; we are building automated TTL + monitoring (see Open Gaps below).
- Account deletions trigger Firestore + Storage cleanup plus an audit log entry.

## Your Choices & Rights

- **Access & export:** A privacy dashboard with export/delete tooling is on our roadmap. Until then, contact us at privacy@crushso.com to request data access or removal.
- **Delete:** You can delete your account from the Safety & privacy screen; this triggers the `deleteAccount` Cloud Function described in the repository.
- **Notifications:** Toggle match, message, and Crush Drop notifications under Settings → Alerts or through system-level push controls.
- **Location:** You can revoke OS-level location access or adjust discovery scopes inside the app; doing so may limit match relevance.

## International Transfers

Data is hosted in Firebase’s U.S. regions. If you access the app from outside the United States, you consent to transferring your data to the U.S., where privacy laws may differ.

## Security

We rely on Firebase’s encryption at rest/in transit, hashed verification codes, Firestore security rules, and internal tooling for least-privilege access. We are implementing audit logging for administrative reads of sensitive collections.

## Children

Crush is for college students 18+ and requires `.edu` verification. We do not knowingly collect data from minors; if we learn we have, we will delete it promptly.

## Changes

We will update this policy when we add new fields, processors, or retention schedules. The “Last updated” date reflects the latest change. Significant updates will be announced in-app or via email.

## Contact

Email hello@crushso.com for questions, data requests, or privacy complaints. If you are in the EU/UK, you may also contact your local supervisory authority.

## Open Gaps & Roadmap

Per `docs/privacy/data-inventory.md`, the following improvements are in progress:

- Automated retention for reports and presence logs.
- Privacy dashboard for export/delete/location precision controls.
- Lifecycle rules for orphaned Storage uploads.
- Admin access logging and monitoring for token purge jobs.

We will update this policy once those controls are live.

