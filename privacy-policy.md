# Privacy Policy

_Last updated: 2026-02-26_

Crush ("we", "us", "our") is a campus connection app that uses SMS login, `.edu` email verification, daily Crush Drop windows (three scheduled reveal minutes per day hashed by time zone), and in-app chat on Firebase + Cloud Functions. There is no swipe deck in the current release. This Privacy Policy explains what data we collect, how we use and share it, and the choices and rights available to you.

## Information We Collect

We collect the following categories of data when you use the app:

1. **Account & Identity Data:** Display name, phone number, `.edu` email, school and state, graduation year, gender + "looking for" by goal bucket, age, Greek-life affiliation, majors, clubs/athletics, connection goals, primary connection goal, relationship preferences (intent, exclusivity, kids, faith importance), roommate preferences (cleanliness, sleep schedule, guests), study preferences (style, time, frequency), gym buddy time and experience preferences, religion, pet preference/has pets, smoking/drinking/going-out style, deal breakers, theme preference, Terms of Service and Privacy Policy acceptance timestamps, and Firebase Auth identifiers.
2. **Verification Data:** Phone verification state from Firebase Auth plus `.edu` email OTP attempts (hashed with a ~10-minute expiry, 1-minute resend cooldown, and school/time zone mapping) stored in the `edu_verification` collection.
3. **Profile Content (UGC):** Bio and profile photos you upload to Firebase Storage, including any optional Greek organization details or interests you add.
4. **Discovery, Location & Presence Data:** Scope selection (campus/state/nationwide) and radius sliders, Crush Drop opt-in, time zone + school time zone, GPS coordinates (with source + timestamp) or campus fallback, Crush Drop window metadata (`dateKey`, `windowInstanceId`, status, scheduled minute, expiry), per-day pairing flags (`crushDropPairedDateKey`, `crushDropPairedIds`), and presence (`online`, `lastActiveAt`). Location and presence live on your profile for pairing and chat experience; they are not included in public profile records or shared outside match-only chat presence.
5. **Engagement & Messaging Data:** Crush Drop activity feed entries, match records (participants, participantInfo display name/school/photo URL, participantInterest, chatUnlocked, pairing intent/connection goal, matchWindow labels/timestamps, and internal ML linkage IDs), pending spotlight queues (`pendingSpotlightMatchIds`), match tombstones (participants + archivedAt) used to prevent repeat matches, chat messages (including image or GIF attachments, attachment metadata like storage paths or Tenor URLs, edit/delete markers, typing indicators, and match-only chat presence heartbeats), drop notification queue entries (ready/reminder/expire), drop pairing queue entries that batch Crush Drop pairing, daily polls (prompt + options by date), poll votes (option selected, campus ID, time zone, timestamp) and campus-only aggregate poll results, pairing event logs (drop impressions/responses/nonresponse/outcomes and sampled candidate-evaluation events) stored in `mlEvents` with pairing intent + score breakdowns, push tokens + last-seen metadata, per-match unread counters and badge counts, messaging rate-limit counters, block lists, archived/hidden match IDs used to hide threads, and account deletion audit logs.
6. **Derived Pairing Features:** Precomputed Crush Drop shortlists (candidate IDs) stored briefly to speed pairing, plus numeric embeddings derived from profile photos and pairing outcomes that help rank candidates and train pairing models.
7. **Safety & Support Data:** Reports, support requests, SafeSearch moderation results and actions for photos, and removal logs for deleted photos.
8. **Device & Usage Data:** App version, device/OS type, Firebase Analytics event identifiers, and basic diagnostics used to monitor delivery of notifications and drops. No marketing trackers are present.

We do not knowingly collect information from individuals under 18; using the app requires a qualifying `.edu` address and college enrollment.

## How We Use Your Information

- **Service delivery:** Authenticate accounts, verify college enrollment, prepare Crush Drop windows (deterministic shared minute per time zone), surface spotlight reveals, process Meet/Pass responses with double opt-in before chat unlocks, and deliver chat via a server-side function with rate limits.
- **Personalization & discovery:** Apply school themes, render profile cards, and respect gender preferences by goal bucket, distance filters, location-based scopes, and the compatibility signals you choose to share (majors/clubs, Greek life, connection goals, relationship/roommate/study/gym preferences, religion, pets, lifestyle habits).
- **Intelligent pairing:** Score potential Crush Drop partners using profile completeness, shared interests, age/grad year proximity, recency/presence, distance, and chemistry factors (including optional lifestyle + connection-goal signals). We may precompute shortlists to reduce drop latency and use derived photo embeddings to improve match ranking while enforcing deal breakers and block lists.
- **Safety & integrity:** Enforce eligibility, deal breakers, and block lists; round public location/presence data; scan photos with Google Cloud Vision SafeSearch and delete/flag when needed; and throttle message sends.
- **Engagement:** Run daily campus polls, record single-select votes, and show campus-only aggregate results after voting.
- **Communications:** Send SMS codes, `.edu` verification emails (via SendGrid), push notifications for drops (ready/reminder/expire), matches, messages, and support responses. No marketing email is sent today.
- **Analytics & diagnostics:** Monitor aggregate usage, notification delivery, and Crush Drop performance, including pairing quality signals used to improve the pairing model.
- **Compliance:** Maintain deletion audit logs, verification evidence, and policy acceptance records needed to operate a college-only network.

## How We Share Information

We do not sell personal data. We share it only with:

- **Service providers:** Firebase (Auth, Firestore, Storage, Analytics, Cloud Messaging, Cloud Functions), Google Cloud Vision, Vertex AI (image embeddings), Tenor (GIF search/hosting), and SendGrid for `.edu` emails.
- **Other users:** Only match candidates and match participants can view your profile cards and photos. Profiles are delivered via Cloud Functions that verify match participation and return short-lived signed photo URLs plus coarse distance buckets. Match records also cache a primary profile photo URL for chat list avatars. Match-only compatibility fields (religion, pets, smoking/drinking/going-out), match-only chat presence heartbeats, and match/chat data (including attachments and typing indicators) are visible only to participants, consistent with Firestore security rules. Daily poll results are shown only in aggregate at the campus level; individual votes are not shared with other users.
- **Legal & safety recipients:** We may disclose information to comply with law, enforce our Terms, or protect the rights and safety of users.

All processors are bound by confidentiality and data protection agreements. We remain responsible for their handling of your data.

## Retention

- `.edu` verification attempts store hashed codes with a ~10-minute expiry and 1-minute resend cooldown; entries are deleted on successful verification or when the code is rejected/expired during a confirm attempt. There is no scheduled sweep for stale entries yet.
- Crush Drop shortlists are cleaned by a scheduled job after ~3 days.
- Drop notification queue entries persist until processed or related matches are deleted; there is no TTL yet.
- Drop pairing queue entries persist until processed and are cleaned after ~7 days.
- Match tombstones remain to prevent repeat matches; retention policy is still being defined.
- Profile, discovery, drop windows, matches, messages, activity entries, reports/support tickets, and token metadata persist while the account is active; there is no automated TTL cleanup today. Invalid push tokens are removed when FCM marks them unregistered, photo removals trigger Storage cleanup, and a daily sweep deletes unreferenced photos from Storage.
- Daily poll prompts, votes, and campus-only aggregates currently have no TTL; they are retained while the account is active unless a purge policy is added.
- Policy acceptance timestamps persist while the account is active and are removed on account deletion.
- Derived photo embeddings are removed when the underlying photo is deleted or when the account is deleted; no separate TTL exists yet.
- Account deletion removes the user document, blocked list, matches + chat threads, drop notifications, activity feed, poll votes, public profile, and Storage photos, logs the request in `account_deletions`, deletes the Firebase Auth user, and leaves match tombstones to prevent repeat matches.

## Your Choices & Rights

- **Access & export:** A privacy dashboard with export/delete tooling is on our roadmap. Until then, contact us at privacy@crushso.com to request data access or removal.
- **Delete:** You can delete your account from the Safety & privacy screen; this triggers the `deleteAccount` Cloud Function described in the repository.
- **Notifications:** Toggle match, message, and Crush Drop notifications under Settings → Alerts or through system-level push controls.
- **Location:** Update GPS or campus fallback in Discovery settings. Revoking OS-level location stops GPS updates; if you stay opted in to Crush Drop we still use your school/campus details for time zone and scope.

## International Transfers

Data is hosted in Firebase’s U.S. regions. If you access the app from outside the United States, you consent to transferring your data to the U.S., where privacy laws may differ.

## Security

We rely on Firebase’s encryption at rest/in transit, hashed verification codes, Firestore security rules that require authentication, match-only presence signals, signed photo URLs for profile cards, cached avatar photo URLs for chat lists, SafeSearch photo moderation, and server-side messaging with rate limits. Admin/audit logging for sensitive reads is planned.

## Children

Crush is for college students 18+ and requires `.edu` verification. We do not knowingly collect data from minors; if we learn we have, we will delete it promptly.

## Changes

We will update this policy when we add new fields, processors, or retention schedules. The “Last updated” date reflects the latest change. Significant updates will be announced in-app or via email.

## Contact

Email hello@crushso.com for questions, data requests, or privacy complaints. If you are in the EU/UK, you may also contact your local supervisory authority.
