# Crush Data Inventory

_Last updated: 2025-12-05_

This inventory lists every place the product stores or processes user data so engineering, product, and legal can reason about consent, retention, access, and safety requirements. Update it whenever you add a new field, collection, queue, or third-party processor.

## How to maintain this file
- When adding a field/collection/service, document **what** you store, **why**, **who can access it**, and **how long** it lives. Link to the relevant code or spec.
- Record both the **current behavior in code** and the **policy you intend to uphold** (e.g., “deleted 30 days after account closure”). If no policy exists, call it out as a TODO.
- Cross-check that privacy policy text, consent copy, and user-facing controls reference the same fields/categories found here.
- Pair every retention or access guarantee with an automated control (Cloud Function, scheduled job, Firestore TTL, etc.).

### Sensitivity legend
- `Basic PII` – identifiers a user expects to share in a social app (display name, school, email, phone).
- `Sensitive PII` – data covered by stricter policies (location, age, gender, verification codes, safety reports).
- `UGC` – user-generated content (photos, bios, chat messages).
- `Operational` – metadata, tokens, or logs required to run the service.

## Data store index
| Store | Location | Primary contents | Sensitivity | Current access surface | Notes / decisions |
| --- | --- | --- | --- | --- | --- |
| `users` collection | Firestore | Profile, preferences, verification state, notifications, location, tokens | Basic + Sensitive + UGC | Owner (per-user), backend functions, admin tooling | Source of truth; see detailed table below. |
| `publicProfiles` collection | Firestore | Sanitized subset of profile fields (display name, photos, bio, verification flags, presence, coarse location) | Basic + limited Sensitive | Signed-in clients, backend | Derived view for drop cards/chat presence; omits phone, email, tokens. |
| `users/{uid}/blocked` | Firestore subcollection | Block list entries | Basic | Owner + backend cleanup | Used for safety + match filtering. |
| `crushDropWindows` (fields on `users/{uid}`) | Firestore | Per-day window metadata (`dateKey`, `status`, `processedAt`), `timeZone`, `schoolTimeZone`, scope overrides | Operational | Owner + backend scheduler + drop UI | Drives the three local-time drops, fuels the campus-first intelligent matcher, and informs client countdowns/spotlights. |
| `swipes` | Firestore | Legacy viewer/target decisions | Operational | Read-only to the viewer; backend retains for audit | Writes disabled; keep until retention policy finalized. |
| `matches` | Firestore | Match metadata, participant info, unread counters, `participantInterest`, `chatUnlocked`, `matchWindow` | Basic + Operational | Client (participants only), backend notifications | Includes double opt-in state, window labels for daily drops, and powers the spotlight reveal shown when a new drop arrives. |
| `messages/{matchId}/thread` | Firestore | Chat messages | UGC | Only participants + moderation tooling | Needs retention + abuse workflow. |
| `reports` | Firestore | Safety reports | Sensitive | Trust & Safety tooling only | Contains free-form text; restrict access. |
| `message_limits` | Firestore | Per-user messaging rate-limit windows (`windowStart`, `count`) | Operational | Backend functions only | Used by server-side messaging guardrails. |
| `support_requests` | Firestore | Help-center tickets | Basic | Support tooling | Same storage story as reports. |
| `account_deletions` | Firestore | Deletion audit trail | Operational + Sensitive | Admin only | Needed for compliance evidence. |
| `edu_verification` | Firestore | `.edu` email verification attempts | Sensitive | Cloud Functions only | Holds hashed codes; TTL required. |
| `activity/{uid}/feed` | Firestore | Drop-ready notifications, interest confirmations, match/message events | Operational | Read-only to the owner; writes by Cloud Functions | No TTL yet; entries remain until account deletion or future cleanup job. |
| (deprecated) `feeds` | Firestore | Pre-ranked swipe deck entries per viewer (candidate IDs, scores, scope metadata) | Basic + Operational | Backend only | Legacy artifact from swipe deck; references removed from the client. |
| `Firebase Storage` | `gs://<project>/users/{uid}/photos/*` | Uploaded profile photos | UGC | Authenticated users only; owner write/delete | URLs stored in `users.photos`, scanned by SafeSearch, deleted on account deletion/photo removal, and swept daily for unreferenced files. Still need lifecycle rules for failed uploads/other buckets. |
| `photo_moderation` | Firestore | SafeSearch results + actions per upload | Operational | Backend/admin tooling | Audit trail for removed/flagged photos. |
| Firebase Auth | Managed service | UID, phone number, auth factors | Basic | Firebase Admin SDK | Must be in privacy policy. |
| Push messaging | FCM/APNs | Device tokens + notification payloads | Operational | Firebase Messaging & Apple/Google | Payload contains match/user display names. FCM tokens that bounce with “not registered” are pruned immediately, and a daily job removes tokens whose `lastSeen` is older than 60 days. |
| Email delivery | SendGrid | `.edu` address + school metadata | Basic | Cloud Function, SendGrid logs | DPAs + unsubscribe flow required. |

---

## Firestore `users` collection
Profiles are created/updated via `ProfileRepository` (`lib/services/profile_repository.dart`) and augmented by notification, presence, and safety services.

Public-facing data is copied into `publicProfiles/{uid}` by `functions/index.js:syncPublicProfile`, so clients only read the sanitized view while the canonical `users/{uid}` document stays owner-only. During this copy, GPS coordinates are rounded to ~100 m precision (and include a `precisionMeters` hint) while presence timestamps are bucketed to 15-minute intervals and `online` only reports activity within the last 2 minutes.

| Group | Fields | Purpose / feature | Sensitivity | Current retention | Notes / gaps |
| --- | --- | --- | --- | --- | --- |
| Identity & enrollment | `displayName`, `phoneNumber`, `eduEmail`, `major`, `gender`, `age`, `graduationYear`, `schoolId`, `stateCode`, `inGreekLife`, `greekOrganizationId`, `greekOrganizationLetters` | Build trust in profiles, enforce campus eligibility, filter by org | Basic + Sensitive | Stored until profile deletion; no automatic pruning | Need user-facing edit + delete controls; document lawful basis (consent vs legitimate interest). |
| Profile content | `bio`, `athletics`, `clubs`, `photos` (+ `photosCount`), `blockedUserIds` | UGC that powers cards and safety filtering | UGC | Same as above | Cloud Function now removes Storage objects + moderation logs when photos are removed; monitor for failures. |
| Discovery preferences | `scope`, `campusRadiusMiles`, `stateRadiusMiles`, `crushDropOptIn`, `timeZone`, `schoolTimeZone` | Controls matching radius, Crush Drop participation, and local drop schedule | Operational + Sensitive (time zone implicitly reveals region) | Until user edits; time zone updates when device/school change | Surface in privacy dashboard with explanations. Document how to disable drops when traveling. |
| Crush Drop windows | `crushDropWindows.{windowId}.dateKey/status/processedAt`, `lastCrushDropAttempt`, `lastCrushDropAt` | Tracks when each drop ran, whether a match was found, and when the scheduler last attempted | Operational | Rolling; overwritten daily | Clients use this to render countdowns/status messages. No historical data retained beyond the current day. |
| Notifications | `notifyMatches`, `notifyMessages`, `notifyCrushDrop`, `fcmTokens` | Delivery controls for push notifications | Operational | Tokens linger until explicitly removed | Add scheduled cleanup for stale tokens. |
| Verification state | `phoneVerified`, `eduVerified`, `updatedAt`, `lastCrushDropAt` | Gate access to features and Crush Drop cadence | Sensitive | Indefinite | Consider logging verification timestamp for audit. |
| Safety & presence | `online`, `lastActiveAt`, `location` (`latitude`, `longitude`, `source`, `updatedAt`) | Presence indicator, location-based feed, harassment mitigation | Sensitive | Location refreshed max every 30 min; stored indefinitely | Add TTL or precision-reduction policy; make opt-out explicit. |

**Access assumptions:** Firestore security rules allow users to read their own full document and signed-in clients to read `publicProfiles` only; all writes for matches/messages/drops go through Cloud Functions, not the client.

## Subcollection `users/{uid}/blocked`
| Fields | Purpose | Sensitivity | Access | Retention |
| --- | --- | --- | --- | --- |
| `displayName`, `photoUrl`, `blockedAt` | Shows who the user blocked and enforces filtering in feed/matches | Basic | Owner + backend cleanup jobs | Lives until user unblocks or deletes account. |

When blocking, the app also updates `users.blockedUserIds` and deletes overlapping swipes/matches (`SafetyRepository`).

## Collection `swipes`
| Fields | Purpose | Sensitivity | Access | Retention / TODO |
| --- | --- | --- | --- | --- |
| `viewerId`, `targetId`, `decision` (`like`/`pass`), `createdAt` | Legacy record of swipe activity | Operational | Viewer read-only; writes disabled globally | Currently only retained for audit/backfill. Plan TTL (e.g., 90 days) or migration to delete after final analytics review. |

## Collection `matches`
| Fields | Purpose | Sensitivity | Access | Retention / TODO |
| --- | --- | --- | --- | --- |
| `participants` (array of UIDs), `participantInfo.{uid}.displayName/schoolId`, `createdAt`, `lastUpdated`, `crushDrop`, `matchWindow`, `participantInterest.{uid}`, `chatUnlocked`, `lastMessage`, `lastMessageSenderId`, `participantUnread.{uid}`, `participantLastRead.{uid}`, `crushDropDeclinedBy` | Inbox list, notification source of truth, audit for harassment cases, double opt-in state | Basic + Operational | Participants + backend functions | Deleted when either participant deletes account or manually ends match (block). Need ability to delete stale inactive matches (policy TBD). |

## Collection `messages/{matchId}/thread`
| Fields | Purpose | Sensitivity | Access | Retention / TODO |
| --- | --- | --- | --- | --- |
| `senderId`, `text`, `createdAt` | Chat between matches | UGC (may contain Sensitive details) | Participants; moderation tooling if needed | Deleted when match removed; no independent retention control yet. Consider message-level report workflow. |

## Collection `message_limits`
| Fields | Purpose | Sensitivity | Access | Retention / TODO |
| --- | --- | --- | --- | --- |
| `windowStart`, `count` per `message_limits/{uid}` | Rate-limit guardrail for `sendMessage` Cloud Function | Operational | Backend functions only | Short-lived metadata; can be pruned automatically or left to TTL once defined. |

## Collection `reports`
| Fields | Purpose | Sensitivity | Access | Retention / TODO |
| --- | --- | --- | --- | --- |
| `reporterId`, `reportedIdentifier` (userId, phone, or message id), `reason`, `details`, `status`, `createdAt` | Trust & Safety investigations | Sensitive | Restricted staff only | Needs lifecycle policy (e.g., keep 2 years) + tooling to update `status`. |

## Collection `support_requests`
| Fields | Purpose | Sensitivity | Access | Retention / TODO |
| --- | --- | --- | --- | --- |
| `requesterId`, `subject`, `message`, `createdAt` | Customer support inbox | Basic | Support tooling | Add response tracking + retention rule (e.g., 1 year). |

## Collection `account_deletions`
| Fields | Purpose | Sensitivity | Access | Retention / TODO |
| --- | --- | --- | --- | --- |
| `uid`, `reason`, `requestedAt` | Audit log proving deletion fulfillment | Sensitive | Admin only | Keep at least 2 years to satisfy regulatory inquiries. |

## Collection `edu_verification`
Maintained only by Cloud Functions, clients never read/write directly.

| Fields | Purpose | Sensitivity | Access | Retention / TODO |
| --- | --- | --- | --- | --- |
| `email`, `domain`, `schoolId`, `stateCode`, `userId`, `codeHash`, `attempts`, `createdAt`, `lastSent` | Issue and validate 6-digit codes for `.edu` addresses | Sensitive (education status) | Cloud Functions; should never be exposed to clients | Records deleted on success/expiry; enforce TTL (firestore policies) for stale attempts. |

## Firebase Storage `users/{uid}/photos`
| Data | Purpose | Sensitivity | Access | Retention / TODO |
| --- | --- | --- | --- | --- |
| Original uploads plus metadata (`contentType`, `storagePath`) | Profile photo gallery | UGC | Authenticated users only (Firebase Storage rules require ID token); owners can write/delete | Account deletion and per-photo removals trigger backend cleanup of Storage objects + moderation logs, a daily sweep deletes unreferenced files under `users/*/photos`, and SafeSearch auto-deletes blatant violations. Still need lifecycle rules for failed uploads/other orphaned files outside that prefix. |

## Collection `photo_moderation`
| Fields | Purpose | Sensitivity | Access | Retention / TODO |
| --- | --- | --- | --- | --- |
| `uid`, `storagePath`, `bucket`, `action` (`flag`/`delete`), `category`, `level`, `safeSearch`, `createdAt` | Audit trail for Vision SafeSearch outcomes and follow-up | Operational | Admin-only tooling | Keep for compliance + repeat-offender tracking; consider TTL once aggregated metrics exist. `flag` entries leave the object in Storage (for manual review) but remove it from the profile; `delete` entries remove the file entirely. |

## Firebase Auth (managed by Google)
| Data | Purpose | Sensitivity | Notes |
| --- | --- | --- | --- |
| `uid`, phone number (primary login), optional email, device metadata, auth history | Authentication + anti-abuse | Basic | Refer to Firebase Auth DPA. Deleting an account via `deleteAccount` Cloud Function also deletes Auth user. |

## Push notification ecosystem
| Component | Data | Purpose / Notes |
| --- | --- | --- |
| Firestore `users.fcmTokens` | Strings per device | Saved by `NotificationService`; used to address push notifications. Invalid tokens are pruned when FCM rejects them. `fcmTokenLastSeen` timestamps are stored but there is no scheduled cleanup yet. |
| FCM/APNs payloads | `title`, `body`, `matchId`, `notificationType`, `senderId` | Set by `functions/index.js` in `onMatchCreated` and chat message triggers; payload content must align with privacy policy. |
| Device OS | Receives payload + stores token | Ensure mobile apps surface notification settings and respect `notify*` toggles. |

## Third-party processors
| Vendor | Data shared | Purpose | Controls / notes |
| --- | --- | --- | --- |
| SendGrid | `.edu` email, school name, verification copy | Emailing verification codes | API key stored as secret; include processor in privacy policy & DPA. |
| Google (Firebase, Maps/Geolocator) | Location coordinates, device identifiers, crash logs | Location service + platform SDKs | Ensure OS-level permissions and in-app copy make usage clear. |
| Apple (APNs) / Google (FCM) | Push tokens + notification payload | Deliver alerts | Respect opt-in/out states. |

### Crush Drop scoring signals

The campus-first matcher evaluates each drop on the fly using existing fields already listed in this inventory (`photosCount`, `lastActiveAt`, `major`, `clubs`, `athletics`, `inGreekLife`, `scope`, `campusRadiusMiles`, `stateRadiusMiles`, and coarse `location`). No separate score documents are stored; values are read, scored in memory, and discarded once a match decision is made. Documenting the signal list here ensures privacy/legal reviewers know which inputs feed the automated pairing logic.

## Open gaps / decisions
1. **Retention:** No automated TTL/cleanup jobs yet for `edu_verification`, matches/messages/activity, legacy `swipes`, or drop notifications; only account deletion clears data today. Define retention windows and implement Firestore TTL/scheduled cleanups (including token aging).
2. **User controls:** Build a privacy dashboard (export/delete data, notification toggles, location precision choice) that maps 1:1 with the fields above.
3. **Access controls:** Document Firestore security rules per collection and ensure Trust & Safety tooling enforces least privilege when reading reports/support tickets.
4. **Storage cleanup:** Account deletion and per-photo removal now delete Storage objects, and a daily sweep removes unreferenced photos under `users/*/photos`. Still need lifecycle rules for failed uploads/other buckets and monitoring for the sweep.
5. **Audit logging:** Log admin access to sensitive collections (`reports`, `support_requests`, `account_deletions`) for compliance audits.
6. **Push token retention:** Add scheduled cleanup based on `fcmTokenLastSeen` timestamps and alerting so invalid/stale tokens are removed even without an FCM error.

Keep this document versioned. When product specs or backend schemas change, update the relevant section and tag legal/safety stakeholders for review.
