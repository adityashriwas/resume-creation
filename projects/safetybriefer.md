# SafetyBriefer — Repository Analysis and Technical Reference

Analysis date: **12 October 2026**. The existing `README.md` is preserved. This document describes the checked-out implementation, not a product roadmap or a claim of production validation.

**Evidence boundary:** The repository contains the Expo/React Native client, native platform projects, configuration, assets, and supporting documents. No backend implementation is present. API behavior below is the contract expressed by client calls and response adapters; database design, server authorization, AI grounding, payment verification, and deployed behavior cannot be verified here. No live API requests, purchases, native builds, or device sessions were performed.

The source inventory contains 443 TypeScript/TSX files, including 50 files under `src/app` (routes, layouts, and special files). These counts describe repository structure, not feature completeness. Relevant workflow implementations, service boundaries, stores, screen compositions, shared UI, and native configuration were inspected. Generated build artifacts, binary assets, and third-party internals were not exhaustively audited.

## Contents

1. [Project overview](#1-project-overview)
2. [Technology stack](#2-technology-stack)
3. [Architecture and folder structure](#3-architecture-and-folder-structure)
4. [Features and modules](#4-features-and-modules)
5. [Application flow](#5-application-flow)
6. [Technical implementation](#6-technical-implementation)
7. [End-to-end feature workflows](#7-end-to-end-feature-workflows)
8. [API integration](#8-api-integration)
9. [Important technical decisions](#9-important-technical-decisions)
10. [Error handling and edge cases](#10-error-handling-and-edge-cases)
11. [Performance, security, and reliability](#11-performance-security-and-reliability)
12. [Setup and execution](#12-setup-and-execution)
13. [Testing and quality](#13-testing-and-quality)
14. [Challenges and improvement opportunities](#14-challenges-and-improvement-opportunities)
15. [Interview preparation](#15-interview-preparation)
16. [Resume-relevant contributions](#16-resume-relevant-contributions)
17. [Knowledge gaps to address](#17-knowledge-gaps-to-address)

## 1. Project overview

SafetyBriefer is a workplace safety application for field users, supervisors/managers, and company/team owners. Its implemented workflows help users prepare task-specific safety meetings, record attendance, document incidents and observations, recognize safe behavior, and review team records.

The primary application is a mobile client with Android and iOS projects. Expo Router also exposes web-oriented shared-meeting views. QR links currently target an external dashboard origin; that deployed dashboard is not implemented by the native screens alone and was not verified.

The app combines:

- AI-assisted meeting content and safety conversation approaches.
- Meeting records, attendee names, host certification signatures, questions, and feedback.
- Six catalog-visible built-in safety forms and server-defined custom forms.
- Four additional local-only incident workflows retained in the route tree.
- Team selection, role-dependent analytics, record history, and exports.
- RevenueCat purchase/restore integration and backend subscription checks.

Safety references and certification warnings are application content. The client does not establish that generated advice is legally compliant, medically accurate, or exclusively sourced from a regulatory website. Existing marketing text making stronger claims should not be treated as verified engineering evidence.

## 2. Technology stack

Versions below are declared in `package.json`; ranges are preserved where relevant. Installed dependency resolution was not checked because `node_modules` is absent.

| Technology | Declared version | Verified usage |
| --- | --- | --- |
| TypeScript | `~5.8.3` | Typed contracts, workflow hooks, components; strict mode in `tsconfig.json`, with remaining `any` assertions |
| React / React Native | `19.0.0` / `0.79.6` | Functional components, hooks, native views, platform branches |
| Expo / Expo Router | `~53.0.26` / `~5.1.8` | Native runtime modules, file-based navigation, stacks, tabs, route parameters |
| TanStack React Query | `5.90.2` | Server queries, mutations, infinite history pages, shared caches |
| Zustand / AsyncStorage | `^5.0.9` / `2.1.2` | Auth/profile state, local records and drafts, team selection, theme, attendee suggestions |
| Expo SecureStore | `^14.0.1` | Native access/refresh session persistence through custom Zustand storage |
| Axios | `^1.13.5` | Shared client factory, bearer authentication, timeouts and one-time unauthorized retry |
| Zod | `4.1.11` | Runtime validation of AI-generated meeting content; not a universal API validator |
| NativeWind / Tailwind | `~4.1.23` / `^3.4.17` | Utility styling, supplemented by inline theme colors and native styles |
| Reanimated / Gesture Handler | `3.17.4` / `~2.24.0` | Screen transitions, loading animation, touch signature drawing |
| Lucide / React Native SVG | `^0.575.0` / `^15.15.1` | Icons, signature paths, manually constructed analytics chart paths |
| Bottom Sheet / Keyboard Controller | `^5` / `^1.19.2` | Attendee-entry sheet and keyboard-aware forms |
| RevenueCat Purchases | `^9.6.7` | Native offerings, account association, purchases, restores, entitlements |
| Expo Camera / ImagePicker / MediaLibrary | SDK-compatible versions in manifest | Android camera modal, photo selection/capture, optional photo-library save |
| Expo FileSystem / Sharing | `~18.1.8` / `~13.1.5` | Attachment staging, image files, PDF/text/CSV exports and share sheets |
| React Native View Shot | `~4.0.3` | Rasterizing drawn signatures to PNG base64 |
| Expo Clipboard / QRCode SVG | `~7.1.4` / `^6.3.21` | Sharing meeting links and rendering QR codes |
| Expo Haptics / AV | `~14.1.4` / `~15.1.4` | Touch/success/error feedback and generation success audio |
| `node-forge` | `^1.3.3` | AES-GCM decryption of remote application configuration |
| ESLint / Prettier | `^9.25.0` / `^3.3.3` | Expo lint configuration, hooks rules, React Query rules; Prettier installed as a declared dev dependency |

`clsx` and `tailwind-merge` implement `src/utils/cn.ts`. `react-native-markdown-display` renders chat content. Date/time pickers support incident forms; safe-area helpers support custom headers.

**Dependency presence is not feature evidence.** The manifest includes many template packages. Source import inspection did not establish active application use of Expo Speech, Expo Location, SQLite, NetInfo, notifications, Victory Native, or Gifted Chat. Analytics charts use SVG directly. No active read-aloud workflow was found despite the existing README's speech claim. AI integration is primarily through backend proxy endpoints, with a direct image-generation compatibility fallback; a dependency/configuration field alone does not establish an active provider integration.

## 3. Architecture and folder structure

The explicit architecture policy is in `CLAUDE.md` and `SRC_ARCHITECTURE_GUIDE.md`: route files remain thin, feature screens compose UI, workflow hooks own orchestration, and services own external integration.

```text
index.ts                         Runtime imports, global CSS, Expo Router entry
src/
  app/                           Route re-exports, root stack, special web files
    (auth)/                      Login, signup, forgot password
    (tabs)/                      Home, history, analytics; hidden two route
    join/[code].tsx              Shared-link redirect
  providers/                     Query client, gestures, keyboard, bottom sheets
  navigation/TabsLayout.tsx       Visible tab registration
  features/
    auth/                        Session, OTP, registration, recovery, login
    account/profile/             Profile API
    app-shell/                   Startup, welcome, onboarding, disclaimer, settings
    billing/paywall/             Purchase UI and subscription integration
    dashboard/                   Home, history, analytics, team selection
    meetings/                    Generate, attend, complete, chat, share, translate
    forms/                       Built-in/custom workflows and submission infrastructure
    exports/                     Backend export downloads and sharing
    meta/                        Industries, states, roles, PPE lookup clients
    teams/                       Team API contracts and operations
  components/common/             Cross-feature UI and submission detail renderers
  services/                      Shared API, AI, device and media services
  theme/                         Theme tokens, persisted mode, color-scheme adapters
  utils/                         Styling, navigation, keyboard, error/config helpers
android/                         Gradle project and Kotlin runtime integration
ios/                             CocoaPods/Xcode project and application delegate
assets/, public/                 Images and audio assets
docs/                            Grouped schema-version-2 submission examples
patches/                         Dependency patch artifacts
```

Feature folders use `screens`, `components`, `hooks`, `services`, `types`, `utils`, `constants`, and `state` where needed. Not every feature needs every folder. For example, `DocumentsScreen.tsx` composes header, lists, informational content, and upgrade UI using `useDocumentsScreen()`.

`src/components/common/forms/submission-detail-view.tsx` dispatches read-only rendering to custom snapshots, recognized built-in renderers, grouped-v2 rendering, or a generic fallback. It is a separate concern from the create-form screens.

The native projects mostly contain Expo/React Native integration, with an important Android permission callback workaround in `android/app/src/main/java/com/safetybriefer/app/MainActivity.kt`. It handles already-granted permissions on Android API 36+ by posting a successful callback to the supplied listener.

Supporting files include historical product/change notes, `API_KEYS.md`, payment notes, a standalone `web-viewer-update.html`, and three encryption diagnostic scripts. They are not authoritative proof of current application behavior. `requirements.txt` begins with a PDF header: it is not a Python package requirements file. `scripts/` is empty in this checkout.

## 4. Features and modules

Paths in this section are relative to `src/features/` unless prefixed otherwise.

| Module | Implemented behavior | Main implementation |
| --- | --- | --- |
| Authentication | Email/password login, email OTP verification before signup, state selection, password recovery | `auth/login/hooks/useLoginScreen.ts`, `auth/signup/hooks/useSignupScreen.ts`, `auth/forgot-password/hooks/useForgotPasswordScreen.ts`, `auth/services/auth-service.ts` |
| Startup and onboarding | Waits for persisted state and auth bootstrap; collects role, industry, PPE and optional team context | `app-shell/onboarding/hooks/useOnboardingScreen.ts`, `src/providers/hooks/useStartupGate.ts` |
| Disclaimer | Local acceptance and timestamp, listener notifications, reset support | `app-shell/disclaimer/services/disclaimer-service.ts` |
| Profile/settings | Fetch/update/reset profile, region-dependent AI prompt reload, theme and sign-out actions | `account/profile/services/profile-service.ts`, `app-shell/settings/hooks/` |
| Home | Topic entry, team switcher, recent meetings, seasonal cartoon, document links, feedback link | `dashboard/home/hooks/useHomeScreen.ts`, `src/components/common/SeasonalCartoonCard.tsx` |
| Meeting generation | Generates structured content, merges PPE, adds keyword warnings/discussion questions, saves a meeting | `meetings/generate/hooks/useGenerateMeetingScreen.ts`, `meetings/generate/services/generate-meeting-service.ts` |
| Attendance/completion | Add/delete attendee names, remember suggestions, host signature upload and completion | `meetings/attendees/hooks/useCollectAttendeesScreen.ts`, `meetings/complete/hooks/useMeetingCompleteScreen.ts` |
| Chat | Question/feedback messaging; native question path requests and saves an AI response | `meetings/chat/hooks/useMeetingChatScreen.ts`, `meetings/services/meetings-service.ts` |
| QR/shared viewer | QR link using backend share code, name-based participation, question submission | `meetings/qr/hooks/useMeetingQrScreen.ts`, `meetings/web/hooks/useMeetingWebScreen.ts` |
| Translation | Fourteen language options, localized labels, Arabic direction metadata, content translation | `meetings/translation/`, `src/components/common/LanguageSelector.tsx` |
| Documents/custom forms | Static built-in catalog plus backend dynamic forms; published-schema rendering and snapshots | `forms/documents/hooks/useDocumentsScreen.ts`, `forms/custom/` |
| History | Two paginated sources merged by date, month grouping, type filters, deletion and exports | `dashboard/history/hooks/` |
| Analytics/teams | Personal/team scope, summary counts, current-month counts, twelve-month charts, team management | `dashboard/analytics/hooks/`, `teams/services/teams-service.ts` |
| Billing | Offerings, plan selection, ownership precheck, purchase/restore, backend sync | `billing/paywall/screens/PaywallScreen.tsx`, `billing/paywall/services/` |

### Built-in forms and implementation status

| Form | Captured information and behavior | Storage/submission evidence |
| --- | --- | --- |
| Injury report | Incident/emergency details, employee, injury/body parts, treatment, witnesses, safety review, photos, reporter signature | `forms/injury/hooks/useInjuryReportForm.ts`; calls `submitInjuryReportSubmission`, then creates a local report |
| Vehicle incident | Three screens covering incident/driver/conditions, vehicles and people/photos, outcome/actions/signature | `forms/vehicle/hooks/useVehicleIncidentPart1Form.ts`, `useVehicleIncidentPart2Form.ts`, `useVehicleIncidentPart3Form.ts`; persisted draft and backend submission |
| Near miss | Stepped incident description, severity/context, photos and reporter signature | `forms/near-miss/hooks/useNearMissReportForm.ts`; attachment upload and `submitFormSubmission` |
| Site safety walk | Walk details, findings/observations, photo documentation and signatures | `forms/site-safety-walk/hooks/useSiteSafetyWalkForm.ts`; grouped backend submission and local state |
| Safety shoutout | Recognition details and photo attachment | `forms/safety-shoutout/hooks/useSafetyShoutoutForm.ts`; grouped backend submission and local record |
| Safety conversation | AI approach selection, employee/supervisor, response/commitments/follow-up, two signatures | `forms/safety-conversation/hooks/useSafetyConversationScreen.ts`; backend submission plus local conversation |
| Forklift/PIT | Equipment, operator certification, contributing factors, injury/damage, checks and corrective actions | `forms/forklift/hooks/useForkliftIncidentForm.ts`; local `createReport` only |
| Loading dock | Dock, inbound/outbound context, trailer/driver checks, injuries and corrective actions | `forms/loading-dock/hooks/useLoadingDockIncidentForm.ts`; local `createReport` only |
| Ergonomic injury | Symptoms/body parts, task factors/metrics and assessment questions | `forms/ergonomic-injury/hooks/useErgonomicInjuryForm.ts`; local `createReport` only |
| Pedestrian incident | Location, incident context, visibility, injuries and corrective actions | `forms/pedestrian-incident/hooks/usePedestrianIncidentForm.ts`; local `createReport` only |

The last four have routes and detail screens but are absent from `forms/documents/constants/documents-screen.tsx`. Their local records are not merged into the current backend-driven History query. Do not describe them as synchronized reporting features.

Custom schemas support text, email/phone/location inputs, numbers, dates/times, yes/no variants, select/multiselect/dependent selects, photos/photo slots, signatures, body parts and repeating groups. Actual rendering is in `forms/custom/components/DynamicFormFields.tsx`; conditional visibility, validation and submission mapping are in `forms/custom/utils/dynamic-form-utils.ts`.

The catalog includes only dynamic records without `replacesFormType` as additional documents. Although replacement metadata exists in types and submission payloads, the current Documents hook does not replace its static built-in list with those records.

## 5. Application flow

### Startup and navigation

`index.ts` imports random-value support, Reanimated, global CSS, and `expo-router/entry`. `src/app/_layout.tsx` mounts `AppProviders`, then the layout shell and `RootLayoutNav` stack. Splash visibility follows startup resolution.

`useStartupGate` subscribes to auth/profile hydration, bootstraps the saved session, mirrors backend profile fields into the meeting store, reads disclaimer acceptance, and derives a target route:

```text
Hydration → session bootstrap
  No authenticated user → Welcome / Login / Signup
  Explicitly signed out → Login
  Missing profile role or industry → Onboarding
  Disclaimer not accepted → Disclaimer
  Otherwise → Home / History / Analytics tabs
```

`RootRedirectScreen.tsx` applies the target via `Redirect`. The root shell also redirects an authenticated-to-signed-out transition to login. This is startup navigation logic, not a demonstrated universal authorization guard for every deep-linked route. Backend enforcement remains essential.

Route groups such as `(auth)` and `(tabs)` organize navigation without becoming ordinary URL path segments. Both `src/app/index.tsx` and `src/app/(tabs)/index.tsx` exist; navigation commonly targets `/(tabs)` explicitly. Treat their shared root URL as a routing ambiguity to validate, rather than assuming the source follows the documented single-root convention.

| Journey | Route sequence / parameters |
| --- | --- |
| Account creation | `/signup` → send/verify email OTP → register → `/login` → `/` |
| Password recovery | `/forgot-password` → email → six-digit code → reset token/new password → `/login` |
| Profile setup | `/onboarding` → save profile → `/` → `/disclaimer` if required → `/(tabs)` |
| Meeting | Home topic → access check → `/generate-meeting?topic=…` → `/collect-attendees?meetingId=…` → `/meeting-complete?meetingId=…` |
| Meeting review | Home/History → `/meeting-detail?meetingId=…` → chat, QR, export or completion actions |
| Shared meeting | External dashboard `/join/{shareCode}`; local `/join/[code]` redirects to `/meeting-web?code=…&lang=…` |
| Encoded viewer | `/view-meeting?m=…&lang=…` decodes content from the URL without fetching the meeting record |
| Forms | `/documents` → built-in route or `/document-custom?id={configurationId}` → success UI |
| Vehicle form | `/document-vehicle` → `/document-vehicle-part2` → `/document-vehicle-part3` |
| Submission review | History → `/submission-detail?submissionId=…` |
| Upgrade | Gated action → `/paywall` with optional `returnTo`, plan, or mandatory parameters |

`src/navigation/TabsLayout.tsx` registers Home, History and Analytics; `two` is hidden with `href: null`. Modal-style stack entries include paywall and meeting QR. Route files generally re-export feature screens; route-level business logic resides elsewhere.

## 6. Technical implementation

### Components, props and hooks

Screen components call a feature hook and pass state plus event callbacks to sections, scaffolds and modals. For example, `CollectAttendeesScreen.tsx` passes attendee input, suggestions and add/delete callbacks into `AddAttendeeModal.tsx`. This separates rendering from orchestration without a custom global event bus.

Shared primitives include `FormControls.tsx`, `FormDetailPrimitives.tsx`, `AppAlertModal.tsx`, `SignatureCapture.tsx`, `AndroidCameraCaptureModal.tsx`, and `LanguageSelector.tsx`. Feature hooks use `useState` for input and modal state, `useRef` for navigation/submission locks, `useMemo` for derivation, and `useEffect`/focus effects for hydration and fetching. Some large screens and hooks still combine multiple concerns; the architecture policy is not uniformly enforced.

### Server state and persistence

`AppProviders.tsx` creates one module-level QueryClient. Provider order is QueryClient → gesture root → keyboard provider → bottom-sheet provider. `AuthQueryCacheBoundary` clears queries when the bootstrapped user ID changes.

Query-key factories exist for meetings, submissions and teams. Meeting details, attendees and messages use related keys; ordinary and infinite meeting lists use different keys to avoid incompatible cache shapes. Team ID is included in list and analytics keys. Some form keys contain the raw session token, which changes cache identity on refresh and unnecessarily places credentials in cache metadata.

Meeting attendee/message helpers update both QueryClient and the persisted meeting store. This enables shared UI to reflect confirmed changes, but creates two representations that must remain synchronized. The current attendee/message hooks use mount/staleness refresh, not WebSockets or periodic polling.

| State | Persistence and lifecycle |
| --- | --- |
| Native auth session | SecureStore contains session tokens; AsyncStorage contains user/company/pending invite/AI configuration |
| Web auth | AsyncStorage stores the persisted auth object; it does not use native secure storage |
| App integration configuration | In-memory `config-store`; loaded after authentication |
| Meeting/profile records | `safety-meeting-store` persists through AsyncStorage |
| Vehicle draft and local form records | Feature Zustand stores; several retain reports and draft data |
| Team UI | Persists selected team, selection lock and view mode; modal visibility remains transient |
| Attendee suggestions | Persisted normalized names, recency and local deletion tombstones |
| Theme | Persisted mode in `src/theme/theme-store.ts` |
| Disclaimer | AsyncStorage acceptance flag/date and in-memory subscriber set |
| Translations | Browser localStorage cache, keyed by meeting ID and language, with 30-day expiry |

Native auth uses a fresh-install marker to clear a previously retained secure session when ordinary app storage has no marker. Bootstrap calls `/me`, attempts refresh after failure, then clears the session if recovery fails. `refreshSession` itself returns false on failure without clearing the session. There is no shared single-flight refresh lock.

Sign-out attempts RevenueCat logout and API logout, then clears auth, resets meeting/team stores and removes a fixed list of storage keys. This is not a complete reset of every already-hydrated feature store; the attendee-directory storage key is also missing from that list.

### Forms and media

Built-in forms commonly use section-specific `canProceed` checks, typed option arrays, platform date/time pickers, and manual async handlers. Their configuration hooks load backend option data. React Query mutations are used in some workflows, while several built-in forms and purchase flows use manual pending state; do not claim all async work follows one mutation abstraction.

Grouped submissions send `schemaVersion: '2.0'`, a `formType`, team context, grouped `data`, attachment metadata, and optional configuration/snapshot information. Attachment fields are represented by stable IDs, such as `reporter_signature`, rather than embedding the entire image in the JSON body.

`uploadFormAttachment` accepts remote URLs unchanged, accepts local file/content paths, or writes base64 into a temporary file. It uploads React Native multipart file metadata, normalizes returned relative URLs against the API origin, and removes temporary files it created. Most calls use 120-second upload timeouts.

`SignatureCapture` records gesture coordinates as SVG paths, renders them, then uses `captureRef` to produce a 1200×400 PNG base64 string. Its error fallback returns JSON path text; downstream upload callers generally expect image data, so this fallback is not format-compatible.

`camera-permission.ts` serializes camera launches, dismisses the keyboard, waits for interactions, requests permission, handles cancellation and optionally saves to the photo library. Failure to save to the library does not prevent attaching the captured image. Android form paths can use the custom CameraView modal.

### AI and runtime configuration

`config-service.ts` fetches an encrypted `{code, tag}` configuration envelope, decrypts it with AES-GCM through `node-forge`, and normalizes provider/RevenueCat configuration. Decryption material is embedded in client code; values are intentionally omitted here. Failure stores an empty configuration instead of blocking startup.

Auth separately retrieves `/ai-prompts`, optionally with a state identifier, and persists the resulting prompt configuration. This is used for safety-conversation prompt/body templates. Changing profile state reloads those prompts.

Meeting generation validates content with Zod and supports direct structured data and several AI response envelopes. Translation and conversation parsing have different, weaker validation approaches. Images first try backend proxy paths, then a direct OpenAI compatibility request if all proxy paths return 404 and remote configuration supplies a key. Model names in source are implementation settings, not a statement about currently available provider models.

## 7. End-to-end feature workflows

### A. Register, sign in and reach the app

1. `useSignupScreen` loads state options, validates email, requests an OTP and verifies it. Registration requires a selected state, verified email, matching passwords and the shared minimum-eight-character password rule.
2. `auth-service.ts` sends registration input and returns a server detail message. Registration does not create the authenticated session in this client; success returns to login.
3. `useLoginScreen` invokes the auth store, which normalizes email and calls `loginUser`. The response adapter accepts camelCase/snake_case variants and unwraps supported envelopes.
4. The auth store saves session, user, company and pending invite; AI prompts load in the background. Login dismisses prior screens and replaces the route with `/`.
5. The startup gate decides whether profile onboarding and disclaimer acceptance are required before tabs.

Backend OTP expiry, registration verification enforcement, password hashing, rate limits and token semantics are not visible.

### B. Generate and complete a safety meeting

1. `useHomeScreen` checks premium access before opening generation. Team members may depend on their team's owner subscription.
2. `useGenerateMeetingScreen` resolves industry/PPE labels, matches topic substrings against `CERTIFICATION_TRIGGERS`, and chooses discussion questions.
3. `generateAndCreateMeeting` posts `{topic, industry, companyRequiredPPE}` to `/meetings/generate` with a 140-second timeout.
4. The service extracts structured output or balanced JSON, validates content/hazards/precautions with Zod, normalizes references and deduplicates company/generated PPE.
5. It separately posts the normalized content, warnings, questions, custom items and team ID to `/meetings`. Generation and persistence are two operations, not one atomic backend transaction.
6. Success upserts the meeting into the local store and selects it as current. Optional cartoon generation/uploads run separately and patch the saved meeting with public image URLs. A cartoon upload can fail while the meeting remains usable.
7. Saving patches custom additions when needed, then replaces the route with attendee collection. A ref lock prevents repeated navigation through this handler.
8. Attendee mutations send names to the backend and update the attendee query and meeting store. The suggestion directory merges backend names and locally remembered names.
9. Completion captures/uploads the host signature, then posts host name/signature and `sentTo: []` to `/meetings/{id}/complete`. Success updates local completion state. This does not prove email delivery or attendee-by-attendee signatures.

### C. Ask a question during a meeting

1. `useMeetingChatScreen` loads meeting details and messages.
2. Sending posts the user message to `/meetings/{id}/messages`; confirmed data is inserted into shared caches and the composer is cleared.
3. For type `question`, a second mutation calls `/meetings/{id}/chat`, then explicitly posts the returned assistant message to `/messages`.
4. An AI failure inserts a local explanatory feedback message. It does not undo the already-saved user question.

The current native hook only triggers AI for `question`. The shared web hook posts questions but does not invoke the AI chat endpoint. Backend-side automatic replies, if any, cannot be inferred.

### D. Submit an injury report

1. `DocumentInjuryScreen` composes stepped UI using `useInjuryReportForm` and `useInjuryReportMedia`. Option data comes through `useFormConfiguration('injury_report')`.
2. Media handlers upload photos/signatures; the form collects incident, employee, medical, witness and safety-review details.
3. `handleSubmitReport` requires an active team, builds grouped v2 data and calls `submitInjuryReportSubmission`.
4. The service maps photo/signature URLs into attachments and delegates to `/forms/submissions`.
5. Only after API success does the hook create the local report and show completion. Errors keep the workflow available and display an error.
6. History later retrieves backend submission IDs; generic submission detail fetches the record and selects a compatible renderer. Export requests the backend PDF.

There is a concrete mapping discrepancy: local injury records include work-absence/restricted-duty fields that are absent from the outgoing grouped payload in this handler. Whether the backend receives those fields by another mechanism was not established.

### E. Fill a custom form

1. Documents loads `/forms/catalog`, filters additional dynamic forms, and routes using a configuration ID.
2. `fetchCustomForm` requests `/forms/custom-configurations?id=…`, normalizes the schema, and rejects unpublished or unavailable forms.
3. `DynamicFieldTree` renders sections/fields. Answers are a local object keyed by field key/ID; conditional branches depend on selected values.
4. `invalidFieldIds` checks required answers, dependent-select details, required photo slots and entries in repeating groups. Valid sections advance the step index.
5. `prepareDynamicFormAttachments` traverses relevant nested fields, uploads photos/signatures, and substitutes attachment references. Uploads are sequential.
6. `buildSubmissionData` uses explicit `dataPath` or section/field keys to construct grouped data. `buildFormSnapshot` includes presentation metadata and field definitions.
7. The mutation posts a custom v2 submission with snapshot, configuration and team metadata. Success shows completion; failure distinguishes attachment versus submission stage and leaves in-memory answers available.

There is no persisted custom-form draft or transaction spanning upload and submission. Snapshot storage preserves the form definition needed to render older records, but backend snapshot retention is not independently verified.

### F. Review history, analytics and exports

History requests meeting and submission pages independently, 25 records per page, combines loaded records, sorts by timestamp and groups by month. Near-bottom scrolling fetches more pages. Filters operate on loaded records; total counts for all/meetings come from server totals, while form-specific counts derive from loaded items.

Confirmed deletion updates the relevant infinite-query pages and removes detail queries. It does not comprehensively invalidate home/analytics caches or remove every persisted meeting copy.

Analytics resolves a selected team and role, derives `me`/`team` scope, fetches summaries and twelve-month trends, and draws SVG paths from normalized series. The variable named `analyticsThirtyDaySummaryQuery` actually uses the current calendar month boundaries, not a rolling 30-day window.

`exports-service.ts` posts an export request, receives a Blob, converts it to base64 through FileReader and writes a local file. Meeting/form exports use `.pdf`, history uses `.txt`, and analytics uses `.csv`. Share helpers then invoke native sharing. File generation itself is server-owned.

### G. Purchase or restore access

1. Paywall queries RevenueCat offerings and backend plans/status. Store package prices drive the displayed price; backend seat counts can replace static feature text.
2. Native configuration selects a test RevenueCat key in development and an Apple/Google key otherwise. Web/native-module/configuration guards can disable purchasing.
3. The screen checks existing RevenueCat identity/subscription ownership with `/subscription/precheck-revenuecat` before associating the backend user ID.
4. Purchase helpers handle cancellation, entitlement checks and Android product-change candidates/proration behavior. Restore checks the supported entitlements.
5. Successful purchase/restore sends subscription metadata to `/subscription/sync-revenuecat` and refetches paywall status. Sync failure is distinct from a store transaction that already succeeded.

Store setup, receipt validation, webhook processing and actual subscription fulfillment are external and unverified. Frontend entitlement checks are not an authorization boundary.

## 8. API integration

All backend paths below are relative to **`<API_ORIGIN>/api/v1`**. Actual deployment origins, tokens, keys and credentials are deliberately omitted. `src/services/api/api.ts` currently defines origins as source constants, not environment variables.

**Authentication notation:** “Bearer” means the client supplies a session token when available; “none attached” describes client construction, not proof that a deployed endpoint is public. Auth endpoints use explicit request credentials/OTP/reset tokens. The shared meeting service uses the same optional bearer client as other meeting operations; anonymous access must be verified against the server.

### Account and configuration

Source: `src/features/auth/services/auth-service.ts`, `src/features/account/profile/services/profile-service.ts`, `src/features/meta/services/`, `src/features/app-shell/config/services/config-service.ts`.

| Method | Path | Input / client expectation | Client auth |
| --- | --- | --- | --- |
| POST | `/auth/register` | Name, email, password, optional company/state; detail message | None attached |
| POST | `/auth/login` | Email/password; session token, refresh token, user/company/invite | None attached |
| POST | `/auth/email-otp/send` | Email; detail | None attached |
| POST | `/auth/email-otp/verify` | Email, OTP; verified boolean | None attached |
| POST | `/auth/password-reset/send-code` | Email; detail | None attached |
| POST | `/auth/password-reset/verify-code` | Email, code; reset token, rejected if missing | None attached |
| POST | `/auth/password-reset/confirm` | Reset token, new password; detail | None attached |
| POST | `/auth/refresh` | `refresh_token`; normalized auth payload | Refresh token in body |
| POST | `/auth/logout` | `refresh_token` | Refresh token in body |
| GET | `/me` | User, company and pending invite | Explicit bearer |
| GET | `/ai-prompts` | Optional `state_id`; AI feature configuration under `data` | Explicit bearer |
| GET | `/me/profile` | Current profile | Explicit bearer |
| PATCH | `/me/profile` | Profile fields including role, industry, PPE/company/region | Explicit bearer |
| POST | `/me/profile/reset` | Reset-profile input; normalized profile result | Explicit bearer |
| GET | `/config` | Encrypted configuration `{code, tag}`, direct or wrapped | Bearer |
| GET | `/states`, `/industries`, `/meta/ppe-options`, `/meta/profile-options` | Lookup options; profile-options client has static fallback | None attached by these clients |

### Meetings and AI

Source: `src/features/meetings/services/meetings-service.ts`, `src/features/meetings/generate/services/generate-meeting-service.ts`, `src/services/ai/ai-service.ts`, `src/features/meetings/attendees/services/attendance-directory-service.ts`.

| Method | Path | Input / client expectation |
| --- | --- | --- |
| GET | `/meetings` | `teamId`, `status`, `page`, `limit`; items/page/limit/total |
| POST | `/meetings/generate` | Topic, industry, company PPE; structured or AI-envelope content |
| POST | `/meetings` | Generated content, hazards, precautions, PPE, references, optional custom/team data; meeting record |
| GET / PATCH / DELETE | `/meetings/{id}` | Fetch, edit fields, or delete a meeting |
| GET | `/meetings/share/{shareCode}` | Trimmed, URL-encoded share code; meeting record |
| GET / POST | `/meetings/{id}/attendees` | List attendees or create attendee from name/role and supported metadata |
| DELETE | `/meetings/{id}/attendees/{attendeeId}` | Delete attendee |
| GET / POST | `/meetings/{id}/messages` | List or add attendee ID/name, message and type |
| POST | `/meetings/{id}/chat` | Question context; adapter expects an AI message result |
| POST | `/meetings/{id}/complete` | Host name/signature, recipients; completion result |
| GET | `/users/attendance` | Previously used attendee names |
| POST | `/ai/chat/completions` | Feature, parsed request/body template and prompt-template metadata; normalized text |
| POST | `/ai/translations` | Prompt and JSON response-format hint; text parsed into translated content |
| POST | `/ai/images/generations` | Prompt, size, quality, format; URL/data URI/base64 image |
| POST | `/ai/images/generate`, `/ai/image/generations`, `/ai/image/generate` | Image compatibility alternatives tried only after 404 |

These clients use bearer token getters. The image fallback instead sends a remotely supplied provider key to the provider's `/v1/images/generations` endpoint, with unauthorized refresh disabled. No provider key is documented here.

### Forms, teams, analytics, billing and exports

Source paths are listed in the final column; all are under `src/features/`. These clients use bearer getters except where a service creates a getter from an explicitly supplied token.

| Method | Path | Input / result | Service |
| --- | --- | --- | --- |
| GET | `/forms/catalog` | First page, limit 50, published status; catalog identity, renderer, icon and configuration metadata | `forms/custom/services/form-catalog-service.ts` |
| GET | `/forms/configurations` | `key`; built-in configuration under `data` | `forms/configurations/services/form-configurations-service.ts` |
| GET | `/forms/custom-configurations` | `id`; normalized published custom schema | `forms/custom/services/custom-form-configurations-service.ts` |
| POST | `/forms/submissions` | Form type, v2 schema, grouped data, attachments, optional snapshot/team; submission result | `forms/shared/services/form-submissions-service.ts` |
| GET | `/forms/submissions` | Form/company/team/status/creator/date/page/limit/search filters; paginated records | Same service |
| GET / DELETE | `/forms/submissions/{id}` | Submission detail / deleted ID | Same service |
| POST | `/uploads/form-attachments` | Multipart `file`; supported URL fields in direct/wrapped result | `forms/attachments/services/form-attachments-service.ts` |
| GET / POST | `/teams` | List teams / create team input | `teams/services/teams-service.ts` |
| POST | `/teams/join` | Join input/code; joined team result | Same service |
| GET / DELETE | `/teams/{id}` | Team detail / deletion | Same service |
| GET | `/teams/{id}/members` | Member list | Same service |
| PATCH | `/teams/{id}/members/{userId}` | Role change | Same service |
| GET | `/analytics/summary` | Team/scope and optional dates; counts/custom forms/incident timing | `dashboard/analytics/services/analytics-service.ts` |
| GET | `/analytics/team-members` | Optional team context; member analytics | Same service |
| GET | `/analytics/trends` | Team/scope/date range; trend labels/series/buckets | Same service |
| GET | `/subscription/status` | Normalized plan/status/access and seat metadata | `billing/paywall/services/subscription.ts` |
| GET | `/subscription/plans` | Plans and seat allowances | Same service |
| GET | `/subscription/check` | `teamId`; owner/team access result | Same service |
| POST | `/subscription/precheck-revenuecat` | RevenueCat app-user ID and platform; ownership/link permission | Same service |
| POST | `/subscription/sync-revenuecat` | Source, platform, user ID and subscription records | `billing/paywall/services/revenuecat-subscription-sync.ts` |
| POST | `/exports` | Discriminated `type`: meeting IDs, form IDs, history filters, or analytics team/scope; Blob | `exports/services/exports-service.ts` |

API availability is not implied by a service export: some services are compatibility helpers and are not necessarily exercised by every screen.

### Request, response and error conventions

`createApiClient` defaults to 60 seconds and JSON headers. Token getters read auth state at request time. A 401 on a non-auth request carrying a token retries once after refresh; `_retry` avoids an infinite retry cycle. Explicit-token clients need special care: a getter capturing an old token or an explicitly set old header can retry stale credentials.

Most services unwrap `data`, normalize fields, and throw `ApiServiceError`. The error helper recognizes nested validation arrays, `detail` strings/arrays and server messages, mapping common HTTP statuses to validation/unauthorized/forbidden/not-found/conflict/server categories. Native networking failures without HTTP status are generally categorized as `unknown`, despite `network` appearing in the declared union.

`requestJson` provides fetch-based timeout/JSON parsing, 204 handling and optional one-time refresh. Its catch path can re-normalize an already normalized error because the latter no longer has `.response`, potentially discarding HTTP status/code. This affects how the image fallback can recognize model errors.

## 9. Important technical decisions

| Choice | Evidence and supported rationale |
| --- | --- |
| Feature ownership with thin routes | Explicit repository architecture instructions; keeps domain logic outside route files and limits unrelated shared UI |
| Shared query client and key factories | Implemented cache sharing; account-change clearing explicitly prevents reuse across changed user identity |
| Hybrid native auth persistence | Storage implementation separates session from larger non-session data; further historical rationale is not documented |
| Form snapshots and attachment IDs | Custom submissions carry their definitions and media references, enabling rendering without relying only on today's catalog |
| Response normalization | Adapters visibly accept multiple naming/envelope conventions; the backend migration history behind them is unknown |
| Backend-generated exports | Client requests files and shares them; rationale for server-side rendering versus local rendering is not documented |
| Backend AI proxies with compatibility fallback | Implemented integration strategy; why a direct provider-key fallback remains is not documented |
| Native purchase guards | Dynamic native import and web checks prevent web from loading an unsupported native module |
| Local store plus server cache | Both exist and are synchronized in selected paths; no complete offline-sync design or conflict policy was found |
| Android camera workaround | Native comment explicitly cites API 36 permission-result callbacks for already-granted permissions |

Do not retroactively claim these choices were made for measured scalability, performance gains or cost reductions; no such evidence was found.

## 10. Error handling and edge cases

**Implemented behavior:** Authentication surfaces field validation and general failure messages. Password reset verifies that a reset token exists. Meeting generation rejects malformed/missing content. Custom forms reject unpublished configurations. Attachment uploads reject responses without a URL. Camera permission denial offers settings navigation. History has loading, error and empty views. Paywall distinguishes cancellation, missing offerings, restore-without-access and backend-sync failures. Several detail screens handle missing local records or failed backend fetches.

**Notable gaps and limits:**

- `useGenerateMeetingScreen.handleShuffleQuestions` launches a promise chain without a rejection handler; save errors are rethrown. Some failures may not receive a coherent UI state.
- Shared web attendee/question handlers are plain async functions without mutation pending/error handling, so repeated presses and failed requests require validation.
- Multiple screens prefer a nonempty cached array over an empty server result (`useMeetingChatScreen`, `useMeetingWebScreen`). A legitimate empty response can temporarily retain stale content.
- Required dynamic text considers whitespace a value, and number/email/phone fields do not receive comprehensive semantic validation in `dynamic-form-utils.ts`. Required photo-slot validation filters blank entries before indexing, potentially changing slot positions.
- Built-in form validation is largely step-specific. Section-jump navigation and final submission should be tested together to ensure earlier required fields cannot be bypassed.
- Injury submission logs a missing team ID and returns without user feedback. Several built-in submit paths do not have a uniform duplicate-submit lock.
- Uploads occur before submission; an interrupted or rejected final request can leave uploaded files unused. Retry idempotency/cleanup is not established by the client.
- Translation cache keys lack a content version/hash. Edited meetings can reuse translations for up to 30 days. Rapid language switching has no clear latest-request guard.
- The custom-form catalog requests only page 1 with a limit of 50; larger catalogs have no continuation fetch in this service.
- Auth bootstrap treats failure broadly; transient network failure plus failed refresh can clear a usable saved session. Refresh concurrency and stale explicit headers can complicate recovery.
- The generic error helper reads Axios-style `.response`; passing an already normalized error back through it may lose detail. UI code that calls it repeatedly should be tested.

## 11. Performance, security, and reliability

### Verified mechanisms

Queries use feature-specific stale times: roughly 30 seconds for several meeting/team queries, one minute for history submissions/analytics, five minutes for catalogs/configuration/subscription views, and a day for selected metadata queries. History loads pages incrementally. Selective queries are enabled only with relevant IDs/auth/role/focus. Team switching preloads appropriate detail/member data with `ensureQueryData`.

Memoization supports sorting/grouping, custom form metadata, selected-team resolution and chart geometry. Navigation refs and some pending-state guards reduce duplicate actions. These are implementation techniques, not measured performance results.

Native session tokens use SecureStore. Account changes clear React Query. Central debug logging requires both development mode and `globalThis.__SAFETY_BRIEFER_DEBUG_API__`; it redacts sensitive keys and truncates payloads. AI meeting JSON is runtime-validated. Attachment staging has best-effort temporary-file cleanup.

### Potential concerns, separated from proven incidents

| Concern | Evidence / implication |
| --- | --- |
| Client-side configuration secrets | `config-service.ts` embeds decryption material and the image fallback uses a provider key on-device. Client-decryptable configuration cannot hide keys from a determined client operator. Server proxying/credential rotation require backend coordination. |
| Account isolation | Logout removes storage keys but resets only selected live stores; attendee suggestions are not removed. Cached private records/names may survive within a process or across users. No actual leak was reproduced. |
| Sensitive local records | Incident details and signatures/media references are stored in ordinary AsyncStorage/local files. No application-level record encryption or comprehensive retention cleanup was found. |
| Web credentials/cache | Web auth uses ordinary persistence, and translations persist browser-side; the native SecureStore guarantee does not apply. |
| Inconsistent logging | `exports-service.ts` and various hooks still use raw console logging outside the central redaction gate. Audit error objects and record metadata before production logging. |
| Subscription interpretation | `normalizeSubscriptionStatus` infers active from any status string outside a short inactive list and ignores the `active` boolean in that path. Unknown strings can be treated as active. Team-check logic separately overrides from a boolean. |
| Role selection | `checkPremiumAccess` derives the highest role across all teams before deciding which subscription to check; mixed roles across teams need explicit tests. |
| Deep links and share codes | Screens and services exist, but deployed anonymous authorization and native association configuration are not established by route names alone. Encoded URL content is not encryption. |
| Cache consistency | Server data is copied into Zustand; deletes and edits do not uniformly invalidate all related caches. Persisted copies can become stale. |
| Large records/exports | History renders accumulated records through scroll content; pagination does not itself provide full virtualization. Blob-to-base64 export processing creates additional memory copies. |
| Permissions/configuration drift | Native manifests contain broad permissions beyond demonstrated features. Android application ID differs from Expo's configured package; reconcile intentionally before regeneration. |
| Safety content quality | Keyword warnings and reference normalization do not validate regulatory correctness. AI responses and translations need domain review; no provenance enforcement was verified. |

No penetration test, privacy assessment, load test, crash-rate measurement or performance benchmark was performed. HTTPS origins in source do not prove certificate pinning, backend access controls or secure server storage.

## 12. Setup and execution

### Repository evidence and prerequisites

This is an npm-oriented Expo project according to `CLAUDE.md`, with both `package-lock.json` and `bun.lock` present. `package.json` does not pin a Node engine/package-manager version. A compatible Node/npm environment and the declared dependencies are prerequisites; no clean installation was performed in this analysis. Lockfile reproducibility and patch application remain unverified.

The declared native scripts require the appropriate platform toolchains. iOS has an Xcode/CocoaPods project and a Podfile default target of iOS 15.1. Android Gradle configuration targets Java/Kotlin JVM 17 and uses SDK values from root project configuration. Native properties enable Hermes and the new architecture.

For a separately provisioned developer checkout, the dependency preparation step is installation from the existing npm manifest/lockfile, commonly `npm ci`. This is documented as a prerequisite, not executed or verified here. The managed workspace rules prohibit installing arbitrary new packages and assign the dev server to Vibecode.

### Commands actually declared

| Package script | Underlying command | Purpose |
| --- | --- | --- |
| `start` | `expo start` | Expo development entry |
| `android` | `expo run:android` | Local Android native run/build |
| `ios` | `expo run:ios` | Local iOS native run/build |
| `web` | `DARK_MODE=class expo start --web` | Web development entry |
| `lint` | `expo lint` | Expo/ESLint checks |
| `typecheck` | `tsc --noEmit` | Strict TypeScript check |

There is no package `test`, production `build`, or deployment script. No application CI workflow or EAS configuration was found in the inspected project inventory. Hosting/store release procedures are not established by this repository analysis. In the managed Vibecode environment, the system owns the development server; these scripts are a reference, not a request to start another server.

### Configuration required for functional workflows

- Backend and dashboard origins come from `src/services/api/api.ts`. No `process.env` references were found under `src`.
- Authentication needs a reachable backend and a usable test account or working OTP registration. Metadata endpoints supply state, industry and PPE options.
- `/config` must provide the encrypted integration envelope expected by the client; `/ai-prompts` supplies feature prompts, optionally by region.
- AI proxies must implement the documented paths/contracts for generation, conversations, translation and images. Empty configuration can leave optional integrations unavailable even though startup succeeds.
- Purchases require a native module/build, valid remote RevenueCat configuration and compatible store offerings; web purchasing is disabled by the client wrapper.
- Camera and photo-library functionality depend on platform permissions. Export/upload code uses native-style FileSystem and multipart conventions; browser behavior needs separate verification.

`API_KEYS.md` describes historical key setup, but current runtime source is the stronger authority. Do not copy real key values, encryption material, signing credentials or deployment URLs into documentation. A configuration-field name is not evidence that its associated provider is actively used.

### Build configuration observations

Babel configures Expo, NativeWind, module aliases and the Reanimated plugin. Metro wraps Expo with NativeWind and the Vibecode SDK, enables inline requires, handles SVG sources and replaces selected native-only modules with empty modules on web. Tailwind scans `src` and uses class-based dark mode with custom gap utilities.

`package.json` includes `patchedDependencies`, while `patches/` includes artifacts for multiple libraries/versions. There is no npm `postinstall` script applying `patch-package`; npm patch application must not be assumed from Bun-style metadata alone. Native/source configuration divergence and retained patch artifacts deserve a clean-build audit in an appropriate environment.

## 13. Testing and quality

**Existing tooling:** ESLint extends Expo flat configuration, enables hooks rules and the TanStack Query recommended rules. TypeScript is strict. Jest, Testing Library for React Native and React Test Renderer are declared development dependencies.

**Existing tests:** No conventional application unit/integration test suite, Jest configuration or package test script was found. `diagnose-iv.js`, `test-short-iv.js` and `verify-fix.js` are standalone encryption diagnostics; they do not establish UI/API/payment test coverage. Their encryption fixtures were not executed or reproduced.

**Checks during this task:** Static source tracing, API-call inventory, module-import inspection, route/store/configuration review and documentation reference checks. Typecheck/lint were not run because `node_modules` is absent. No packages were installed. No runtime, backend, browser, emulator or device success is claimed.

The architecture guide specifies `npx tsc --noEmit` and `npx expo lint`, plus an optional unused-symbol check. These should be run in a provisioned development environment; passing them would still not verify backend authorization or native integrations.

Recommended targeted coverage:

- Auth hydration, concurrent 401s, explicit-header retry, offline bootstrap and cross-account logout cleanup.
- AI response parsing across structured/enveloped/fenced/malformed outputs and generation-success/save-failure cases.
- Dynamic conditional/repeating fields, whitespace, required photo slots, attachment paths and snapshot rendering.
- Vehicle draft transitions and built-in form final validation, duplicate presses and upload failure.
- History pagination/filter counts, deletion propagation, empty server arrays and team switching.
- Subscription status allowlist, mixed team roles, cancellation, restore, ownership conflict and sync failure after purchase.
- Android camera permission return path, native signature capture, large exports and web upload/share compatibility.

These are recommendations, not tests already present.

## 14. Challenges and improvement opportunities

| Priority | Concrete issue | Suggested follow-up / relevant files |
| --- | --- | --- |
| High | Integration keys reachable by the client | Audit remote config and image fallback; move privileged provider calls server-side. `src/features/app-shell/config/services/config-service.ts`, `src/services/ai/ai-service.ts` |
| High | Incomplete cross-account cleanup | Centralize account-scoped state reset and include attendee-directory/translation/media retention. `auth-store.ts`, feature stores, `translation-cache.ts` |
| High | Refresh races and stale credentials | Share one refresh promise, retry with the latest token, distinguish network failure from invalid session. `interceptors.ts`, `auth-store.ts`, explicit-token services |
| High | Weak subscription status interpretation | Define accepted active statuses and select role/access per active team. `subscription.ts` |
| High | Local/backend injury payload mismatch | Map absence/restricted-duty values into the server contract or explicitly remove unsupported fields from the workflow. `useInjuryReportForm.ts` |
| High | Signature fallback is not image data | Surface capture failure and retry instead of sending JSON as PNG/base64. `SignatureCapture.tsx` |
| Medium | Four local-only forms outside catalog/history | Decide whether to integrate, intentionally expose locally, or retire them; do not silently describe them as synchronized. Form hooks/stores and Documents/History hooks |
| Medium | Partial two-stage submissions | Introduce server idempotency/attachment ownership and cleanup; preserve recoverable drafts. Form attachment and submission services |
| Medium | Multiple representations of server state | Clarify authoritative state, invalidate home/history/analytics together, remove deleted local records. Meeting helpers and `useHistoryDelete.ts` |
| Medium | Dynamic validation/data-path trust | Validate schema and answer types, nested conditions, numeric/email semantics, path segments and attachment-slot identities. `dynamic-form-utils.ts`, `custom-form-submission.ts` |
| Medium | Error normalization loses metadata | Preserve existing `ApiServiceError` fields and explicitly classify network failures. `api-errors.ts`, `request.ts` |
| Medium | Mixed pending/error patterns | Migrate selected plain async handlers to consistent mutation states and prevent repeated submissions. Built-in forms, shared web hook, paywall |
| Medium | History counts/filtering cover only loaded subsets | Fetch filtered pages/counts server-side or label loaded counts accurately. `useHistoryQueries.ts` |
| Medium | Translation staleness and race conditions | Include content version/hash and guard against out-of-order language results. Translation cache and viewer hooks |
| Medium | Native/web and native/Expo drift | Verify platform behavior, reconcile intended identifiers/permissions, validate required patches in a clean install. Native projects, Metro, manifest/lockfiles |
| Medium | Root URL and access assumptions | Validate both index routes and deep-link access with actual router behavior; enforce protection deliberately. `src/app/`, `useStartupGate.ts` |
| Low | Documentation and template drift | Reconcile historical speech/provider/signature claims, unused dependencies and misleading `requirements.txt` extension. README, package manifest, support documents |

Priorities are engineering recommendations based on source evidence, not a production incident ranking. No fixes were made during this documentation task.

## 15. Interview preparation

Use these answer outlines with the referenced code. Be explicit about what you personally implemented and what was inherited.

| Question | Code-grounded answer outline |
| --- | --- |
| How is the app organized? | Expo Router files delegate to domain screens. Screens compose UI; hooks coordinate state/navigation; services isolate APIs. Point to Documents or attendee collection. |
| What happens before the home screen appears? | Auth/profile hydration, `/me` bootstrap with refresh recovery, profile completeness and disclaimer checks; root redirect selects the destination. Explain why async hydration must not be treated as logged out. |
| Why use React Query and Zustand together? | Query handles remote loading/cache/mutations; Zustand persists session/UI/drafts/local records. Acknowledge duplicated meeting state and synchronization costs. |
| How do props and callbacks connect the UI? | `AddAttendeeModal` receives values/suggestions and callbacks from the screen hook; it handles sheet/keyboard presentation without owning the meeting API. |
| How are query keys designed? | Separate list/detail/attendee/message and infinite-list shapes; team identity scopes data. Token-containing form keys are an improvement opportunity. |
| Explain token refresh. | Axios intercepts authenticated non-auth 401s, marks `_retry`, refreshes, then reissues. Discuss missing single-flight control and stale explicit-token headers. |
| Where are credentials stored? | Native sessions in SecureStore; non-session auth in AsyncStorage; web uses ordinary persistence. Runtime config has a separate client-side secret exposure concern. |
| What occurs during sign-out? | Best-effort external logout, local auth reset, selected store resets, key removal and user-change query clearing. Other live stores and attendee suggestions need a fuller reset. |
| How does meeting generation work? | Entitlement check → metadata/warnings → generation API → parse/Zod → separate create API → local upsert → attendee flow. Explain the failure window between generation and creation. |
| Why isn't TypeScript enough for AI output? | Network responses are untrusted runtime values. Zod verifies meeting shape; string parsing alone cannot prove valid content or regulatory accuracy. |
| How does JSON extraction handle braces in strings? | The meeting parser tracks nesting, string state and escapes before parsing a balanced object. Markdown fences are stripped first. |
| How do dynamic forms work? | Server schema drives field rendering and conditions; answers map by identity/dataPath; attachments upload first; submission stores a snapshot for later rendering. |
| How are nested answers updated safely? | Hooks generally use functional setters and shallow copies at the changed collection/object level. Explain reference identity and why mutating a shared nested object can bypass updates. |
| How is a signature captured? | Gesture points become SVG paths; view-shot rasterizes PNG/base64; attachment service uploads it. The JSON fallback is a known incompatible path. |
| What differs between native and web? | SecureStore versus web persistence, guarded RevenueCat native import, localStorage translation cache, FileSystem/multipart assumptions and native camera/UI modules. |
| Is chat real-time? | Current hooks fetch/cache messages; no socket or periodic polling is established. Native questions invoke an AI request and then save its reply. |
| Are attendees individually signing? | Current collection and shared viewer collect names; the host signs on completion. Do not repeat the historical README's broader signature claim. |
| How do you prevent double actions? | Mutation pending flags and selected ref locks. Coverage is uneven; injury/shared web handlers are examples to review. |
| What do useRef/useMemo/useCallback accomplish here? | Ref preserves non-rendering locks; memo derives grouped records/chart paths; callback stabilizes handlers where needed. They do not automatically make rendering fast. |
| How would you debug stale history after deletion? | Inspect server result, infinite-query key/page update, detail removal, home/analytics invalidation and persisted meeting copy. Identify which representation the screen reads. |
| Why can filtered counts differ from total? | Two independent paginated sources are merged; form-specific counts cover loaded items while server totals describe the full result set. |
| Explain Promise.all versus allSettled in this app. | Both run independent work concurrently; all rejects on a rejection, while allSettled supports partial refresh outcomes. Analytics refresh uses allSettled. Neither inherently cancels the underlying work. |
| Does Promise.race cancel an export? | No. UI timeouts can reject first while the underlying request continues; transport timeouts are separate. Cleanup/AbortController would need explicit handling. |
| How is premium access determined? | Backend status/check plus team role/context; RevenueCat handles native purchase state and sync. Unknown statuses and highest-role-across-teams logic deserve tests. |
| What if a purchase succeeds but backend sync fails? | The transaction may already exist. Surface sync failure, reconcile/restore safely, and avoid assuming the user needs a second purchase. Server verification is outside this repo. |
| What are the main security boundaries? | Backend must authorize records/teams/subscriptions and protect provider credentials. Client UI checks, encryption with embedded keys and encoded links are not security boundaries. |
| What works offline? | Previously persisted local state and local-only report creation may remain available. No complete offline request queue/replay/conflict-resolution system is implemented. |
| How would you test this project first? | Pure parsers/mappers, auth races, form attachment mapping, subscription status normalization and account cleanup; then native device workflows. Existing test dependencies are not evidence of coverage. |
| What does the Android native customization solve? | API 36+ already-granted permission requests can miss callbacks; `MainActivity` posts the result when all requested permissions are granted. Verify on affected devices. |
| What cannot you claim about the backend? | Database schema, AI retrieval/grounding, receipt validation, access-control enforcement, rate limiting, retention and email delivery are not visible. |

Suggested practical exercises: trace one injury field from input to payload and renderer; simulate a 401 during two concurrent requests; model a photo upload succeeding before submission times out; explain a mixed-role user changing teams; and inspect how an empty server array interacts with local fallback state.

## 16. Resume-relevant contributions

The repository supports the following engineering topics. These are **candidate descriptions**, not evidence that any specific person owned the work. Use only the portions you actually implemented, reviewed or maintained, and adjust the verb accordingly.

- Built or maintained feature-based Expo/React Native workflows using TypeScript, Expo Router, reusable UI sections and domain hooks.
- Integrated email/password authentication, email verification, password recovery, session refresh and native secure session persistence.
- Implemented AI-assisted safety meeting generation with runtime response validation, backend persistence, PPE merging and task-triggered warning UI.
- Developed multi-step incident forms with camera/photo uploads, gesture-based signatures, grouped submission payloads and historical detail rendering.
- Implemented schema-driven custom forms with conditional fields, repeating groups, attachment references and persisted form snapshots in submission requests.
- Integrated paginated meeting/form history, record deletion, team-scoped analytics and PDF/text/CSV export workflows.
- Implemented meeting sharing through QR codes, attendee-name collection and multilingual shared views.
- Integrated RevenueCat offerings, purchase/restore workflows, ownership prechecks and backend subscription synchronization.
- Addressed mobile keyboard, safe-area and Android permission-callback behavior across native workflows.

A defensible project summary is: “A React Native workplace-safety client integrating authenticated APIs, AI-assisted meetings, configurable reporting forms, team analytics and native subscription workflows.”

Do not claim measured time savings, adoption, revenue, reduced incidents, regulatory compliance, complete offline support, production-scale performance, comprehensive test coverage, backend implementation ownership or end-to-end security without separate evidence. Local-only form workflows and unverified deployment behavior should be qualified when relevant.

## 17. Knowledge gaps to address

| Priority | Concept | Why it matters here / starting point |
| --- | --- | --- |
| 1 | Auth lifecycle, token rotation and concurrency | Bootstrap/refresh/storage/account isolation; `auth-store.ts`, `interceptors.ts` |
| 1 | Server state versus persisted client state | Understand query keys, invalidation, stale copies and user/team boundaries; meeting helpers, `AppProviders.tsx` |
| 1 | Trust boundaries and client secrets | Embedded decryption material, provider fallback, backend authorization; config and AI services |
| 1 | API contracts and runtime validation | Distinguish TypeScript assertions, Zod checks and normalization; generation/form/auth services |
| 1 | React hooks and asynchronous JavaScript | Effects, stale closures, functional updates, refs, races, cancellation and partial failures in workflow hooks |
| 2 | Dynamic schema design and validation | Conditions, repeating groups, field identities, versioned snapshots, data-path safety and attachment mapping |
| 2 | Native media and storage | Camera permissions, file/content/data URIs, base64 cost, signatures, privacy and file cleanup |
| 2 | Subscription lifecycle | Store entitlements, account identity, restores, proration, pending sync and server receipt verification |
| 2 | Expo Router and deep links | Route groups, duplicate root resolution, modal navigation, startup gates and external link associations |
| 2 | Testing strategy | Pure-function tests, mocked API boundaries, component tests and real-device integration scenarios |
| 3 | Rendering and large-data performance | Scroll versus virtualization, memoization limits, image/export memory, SVG chart derivation |
| 3 | Localization and date semantics | Translation race/cache design, RTL, calendar month versus rolling windows and timezone conversions |
| 3 | Native build reproducibility | Hermes/new architecture, Gradle/CocoaPods, Expo/native drift, package managers and dependency patch application |

### Analysis exclusions and unresolved evidence

No backend source, server schema, deployment dashboard source, production logs, store configuration or live provider responses were available for verification. `node_modules` was absent, so dependency implementation and executable quality checks were unavailable. No permission-denied source area blocked the analysis.

Binary image/audio/PDF contents, generated Android/iOS build output, `.expo` artifacts, editor metadata and compiled material embedded in dependency patches were excluded from detailed behavior analysis. Patch inventory and relevant configuration were inspected without modifying them. Historical prose and the standalone web viewer were treated as supporting context, not confirmation of deployed features. Git and the managed dev server were not inspected or changed.

Only this new documentation file was created; application source, dependencies, native configuration and the existing README were preserved.
