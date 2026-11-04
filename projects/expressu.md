# ExpressU — Repository Analysis and Interview Reference

This document describes the current working tree reviewed on **12 October 2026**. The existing `README.md` is preserved. Implementation files take precedence over earlier documentation and the bundled API specification.

**Evidence labels:** “Implemented” means a reachable frontend implementation was traced, not that its backend or device behavior was tested. “Partial” identifies missing wiring or placeholder behavior. “Potential issue” is a source-based finding requiring a focused reproduction. Recommendations are future work, not existing achievements.

## 1. Project Overview

ExpressU is a React Native CLI mobile application for Android and iOS, internally named `expressme`. Its implemented interface supports caregivers and care receivers communicating through predefined or customized signals inside groups. It provides a simpler interaction than typing a full message: a receiver selects a need or emotion, sends it to a group, and can inspect subsequent acknowledgement and reader information.

Caregivers manage groups, members, signal definitions and recurring prompt configuration, and review message activity. Regular groups support custom labeled color signals; caregiving groups provide mode-specific signal legends. These interfaces indicate the intended users and communication problem; adoption, clinical suitability, customer requirements and business outcomes are not established by this repository.

This is a **mobile frontend with native platform projects**, not an Express.js server despite the directory name. There is no backend application, database schema, queue worker or scheduler implementation here. `api-docs.yaml` describes an external API; Reverb-compatible broadcasting and Firebase Messaging are external integrations. No emergency-service dispatch is implemented by the signal labeled SOS.

## 2. Technology Stack

Versions below are declarations in [package.json](package.json), unless stated otherwise. The npm lockfile also resolves React Native to `0.85.0` and React to `19.2.3`.

| Technology | Version/declaration | Verified usage |
| --- | --- | --- |
| React Native / React | `0.85.0` / `19.2.3` | Native screens, hooks, lists, images, modals and platform APIs |
| TypeScript | `^5.8.3` | TS/TSX application, typed navigation, models and service payloads; some casts and loose records remain |
| JavaScript | — | Entry point, tooling configuration and style inspection script |
| React Navigation | Native `^7.2.2`, native stack `^7.14.11`, bottom tabs `^7.15.9` | Authentication stacks, caregiver group stack, shared settings stack and role tabs |
| TanStack Query | `^5.99.2` | Group queries, mutations, cache updates/invalidation, focus and network integration |
| Axios / native `fetch` | `^1.15.2` / runtime | Axios for most REST calls; `fetch` for group creation, account deletion and broadcast authorization |
| Pusher JS | `^8.5.0` | React Native WebSocket client configured for Reverb-compatible private channels |
| Firebase app / messaging | `^24.0.0` | Device token registration, permission requests, push-open listeners and background callback |
| AsyncStorage / Keychain | `^3.0.2` / `^10.0.0` | Profile/preferences/device token versus protected auth token/session storage |
| NetInfo | `^12.0.1` | Reachability state, offline banner and Query `onlineManager` |
| Reanimated / Worklets | `4.4.1` / `^0.9.1` | Custom bottom-sheet shared values and animations; Worklets is supporting runtime infrastructure |
| Gesture Handler | `^2.31.1` | Entry initialization, root wrapper and native interaction integration |
| React Native Paper | `^5.15.1` | Theme provider and shared input/button wrappers |
| Safe Area Context / Screens | `^5.5.2` / `^4.24.0` | Insets and native-screen integration |
| Image Crop Picker | `^0.51.1` | Camera/library selection, cropping and upload asset conversion |
| Vector Icons / SVG | `^10.3.0` / `^15.15.4` | Icons and onboarding vector artwork |
| Date Picker | `^5.0.13` | Shared `DatePickerField` implementation; no active feature caller found |
| Gorhom Bottom Sheet | `^5.2.9` | Installed, but active group sheets use the custom implementation; do not attribute their behavior to Gorhom |
| Kotlin / Swift / Objective-C | Kotlin `2.1.20` configured | Android host/channel creation, iOS host/Firebase setup, custom iOS camera permission bridge |
| Jest / ESLint / Prettier | `^29.6.3` / `^8.19.0` / `2.8.8` | Tests, React Native lint configuration and formatting configuration |
| Metro / Babel / Gradle / CocoaPods | Repository configurations | JS bundling, transformation, Android build and iOS dependency integration |

`@react-native/new-app-screen` is installed but is not the active app UI. There is no Redux store, Zustand store, Formik or schema-validation framework in the application flow.

## 3. Architecture and Folder Structure

```text
index.js                         Native registration, typography, background FCM handler
App.tsx                          Re-export of src/App
src/
  App.tsx                        Providers, NavigationContainer, linking, error logging
  navigation/                    Auth/role/settings routing and notification navigation
  providers/                     Query, alerts, network, Reverb, receiver text scale
  features/
    auth/                        Forms, recovery screens, auth services/mutations/types
    caregiver/groups/            Group creation, administration, timeline, service adapters
    careReceiver/                Signal home, activity log, reader sheet, placeholder details
    shared/settings/             Profile editing, account deletion, settings destinations
  services/
    api/                         Axios, refresh, auth recovery, storage adapters, errors
    realtime/                    Pusher client, event normalization, cache synchronization
    notifications/               Firebase device token lifecycle
    permissions/                 Camera permission service
    storage/                     AsyncStorage and Keychain wrappers
  components/common/             Inputs, buttons, custom sheets/portal, headers, offline UI
  theme/                         Lexend font families and global typography setup
  utils/                         Dates, image assets, reader labels, style constants
  assets/                        Signal icons, onboarding images, bundled fonts
  types/                         Shared library declarations
android/                         Gradle project and Kotlin host
ios/                             Xcode workspace/project, Podfile and native host/module
__tests__/                       App smoke test and API error-message unit tests
scripts/find-unused-styles.js    Read-only heuristic style-reference inspection
api-docs.yaml                    API contract; not backend source
PUSH_NOTIFICATIONS_SETUP.md      Existing integration guidance; verify against current code
```

The prevailing dependency direction is **service → hook → screen/component**. For example, `groupsApi.ts` performs requests, `useSendGroupMessageMutation.ts` updates caches, and `HomeScreen.tsx` handles user interaction. Exceptions include custom pagination hooks and profile/group-detail reads that call services directly rather than using `useQuery`.

Feature types distinguish backend payload names from UI models. `groupsMappers.ts` converts identifiers to strings, normalizes alternative field names, and constructs group-card/member/message data. Shared group APIs live under the caregiver feature even though receiver screens and global providers also import them, creating cross-feature coupling.

### Bootstrap and provider responsibilities

1. [index.js](index.js) initializes Gesture Handler, runs `setupTypography()`, installs Firebase's background-message callback and registers the application.
2. [src/App.tsx](src/App.tsx) enables native screens and nests Safe Area → Gesture Handler → app providers → Paper → network → bottom-sheet portal → navigation.
3. [AppProviders.tsx](src/providers/AppProviders.tsx) supplies the singleton Query client and app alerts; `AppState` updates Query's focus manager.
4. [RootNavigator.tsx](src/navigation/RootNavigator.tsx) restores auth, displays a currently empty splash component while restoring, and chooses the unauthenticated or role-specific branch.
5. `ReverbProvider` exists within the authenticated branch. `ReceiverTextScaleProvider` exists within receiver tabs. Push initialization is enabled when a session exists.

## 4. Features and Modules

| Module | Implemented behavior and limits | Main source references |
| --- | --- | --- |
| Authentication | Shared sign-in/sign-up form, field errors, password visibility through inputs, async submission, stored auth; signup sends name/email/password, not a selected role | `features/auth/components/AuthFormScreen.tsx`, `services/authApi.ts`, `hooks/useAuthMutations.ts` under `src/features/auth/` |
| Password recovery | Submit email, enter six-digit numeric OTP, then submit matching passwords; no reset proof is carried in navigation beyond email | `ForgotPasswordScreen.tsx`, `VerifyResetOtpScreen.tsx`, `ResetPasswordScreen.tsx` under auth screens |
| Group dashboard | Group cards with latest activity, unread counts, blocked appearance, loading skeleton, empty list and pull-to-refresh | `src/features/caregiver/groups/screens/GroupsDashboardScreen.tsx`, `components/GroupCard.tsx`, `hooks/useCurrentUserGroupsQuery.ts` |
| Group creation | Multi-screen draft; regular custom signals or caregiving template; email-based members; prompt configuration | `src/navigation/caregiver/GroupsStackNavigator.tsx`, group screens `GroupDetailsScreen.tsx`, `CreateGroupScreen.tsx`, `GroupTemplateScreen.tsx`, `GroupMembersScreen.tsx` |
| Group administration | Owner-gated name/photo edits, add/remove members, prompts, custom signals, delete group; member exit/block/unblock actions depend on identity/membership | `src/features/caregiver/groups/hooks/useGroupAdminDetails.ts`, `screens/GroupAdminDetailsScreen.tsx`, `components/GroupAdminDetailsSections.tsx` |
| Signal timeline | Paginated messages, day headings, sender information, acknowledgements, optimistic regular-group send, live events, unread jump control, requested message highlighting | `src/features/caregiver/groups/screens/GroupOverviewScreen.tsx`, its helper files and timeline hooks/components |
| Receiver home | Chooses a group, renders mode-specific/custom signals, confirms sending, shows five recent messages and reader information | [HomeScreen.tsx](src/features/careReceiver/screens/HomeScreen.tsx) |
| Receiver activity | Paged history for the selected group, refresh on certain realtime events, reader sheet, list virtualization | [ActivityLogScreen.tsx](src/features/careReceiver/screens/ActivityLogScreen.tsx) |
| Read information | Separate mark-group-read operation, acknowledgement operation and message-reader lookup; these are distinct concepts | Group API functions, `MessageActivityCard.tsx`, `MessageReadersSheet.tsx`, `src/utils/readerSummary.ts` |
| Profile | Edit full/display name and avatar; account email is displayed read-only; camera/library cropping and multipart update | `src/features/shared/settings/screens/EditProfileScreen.tsx`, `components/EditProfileForm.tsx`, `services/profileApi.ts` |
| Account lifecycle | Confirm account deletion, call API and sign out; logout attempts device unregistration and server logout then local cleanup | Shared `SettingsStackNavigator.tsx`, `RootNavigator.tsx` |
| Accessibility-oriented UI | Receiver text scaling persisted locally, safe-area handling, labeled/icon signals and selected accessibility props | `src/providers/ReceiverTextScaleProvider.tsx`, common controls and receiver styles |
| Connectivity | Offline banner with retry and query online/focus integration | `src/providers/NetworkProvider.tsx`, `src/components/common/OfflineBanner.tsx` |

### Signal and prompt semantics

[groupSignalDefinitions.ts](src/features/caregiver/groups/utils/groupSignalDefinitions.ts) defines nine non-verbal slots, including Mad/Angry, Scared, Hungry, Bathroom, Outside, Affection/Play, SOS, Neutral and Thirsty. Elder mode has eight slots, and Safe mode reuses the elder definitions. Slot meanings therefore depend on mode. Current home code uses `getCareGroupSignalDefinitions`, not the separately exported receiver definition array. A shower icon asset does not establish a reachable shower action.

Regular-group custom signals are capped at eight slots. Admin editing requires a nonempty label and a six-digit hex color. Prompt editing supports up to three prompts; custom schedules accept integer hours from 1 to 24 and are converted to `frequency_minutes`. This repository configures schedules through the API; it does not implement their server execution or prove reminders arrive on time.

### Partial, placeholder and unconnected functionality

- About, Privacy Policy, Terms and Help screens currently render their headers without substantive content.
- `RequestDetailsScreen.tsx` is explicitly a placeholder. Home receives `onOpenRequest` as `_onOpenRequest` and does not use it, so this is not an active request-detail journey.
- `EmoMeScreen.tsx`, `AddMembersScreen.tsx`, `CaregiverMembersScreen.tsx`, the standalone `DeleteAccountScreen.tsx`, and `useInfiniteGroupMessagesQuery.ts` exist but are not wired into the active flows found. Account deletion instead uses a settings confirmation alert.
- Group creation supplies no-op `onShareLink` callbacks. An invitation claim/accept screen is not wired, even though invitation endpoints exist in the YAML contract.
- A silent-mode value is read from group/push data, but no active silent-mode editing workflow was found.
- Foreground push callbacks currently return without displaying UI; background JS handling logs the message. Push-open navigation is implemented separately.

## 5. Application Flow

```text
Native launch → index.js → src/App.tsx → restoreStoredAuthState()
  No valid session → Onboarding → Sign In / Sign Up
    Forgot Password → Verify Reset OTP → Reset Password → Sign In
  careGiver session → Groups tab + Settings tab
    Groups list → Group overview → Group administration
    Create → Name/type
      Regular → Custom actions → Members → Submit → Groups list
      Caregiving → Template → Receiver/caregivers/prompts → Submit → Groups list
  careReceiver session → Home tab + Activity tab + Settings tab
    Home → Signal confirmation → Send → Recent activity
    Home → Group administration/details
    Activity → Paged history → Reader sheet
  Settings → Edit Profile / About / Privacy / Terms / Help
    Delete account confirmation or Logout → Unauthenticated branch
```

Auth and caregiver/settings stacks use React Navigation native stacks. Receiver `HomeStackNavigator` instead uses local `route` and `selectedGroup` state, while `ActivityStackNavigator` is a view wrapper. Consequently, not every component called “StackNavigator” supplies native stack history or hardware-back handling.

Navigation adapters pass callback props into screens, keeping many screens independent of navigation objects. Creation state remains in `GroupsStackNavigator` across steps and resets after successful creation. Some tab parameters carry callbacks such as logout and tab-bar visibility; these are nonserializable values and complicate navigation persistence/linking.

The JS linking configuration declares `expressme://` and an example HTTPS prefix, mapping `groups/:groupId` to caregiver group overview. Native Android VIEW filters and iOS URL type/universal-link registration were not found in the inspected manifest/plist/delegate. Treat external deep links as **partially configured**, not verified device functionality.

## 6. Technical Implementation

### Components, hooks and communication

`useState` holds form drafts, local route state, modal visibility, pending actions and page results. `useMemo` derives signal legends, sorted messages, display lists and scaled styles. `useCallback` stabilizes handlers where used. `useRef` tracks current focus, in-flight requests, loaded cursors, latest callbacks and scrolling without requiring a render for every change.

Contexts expose alerts, connectivity, realtime client and receiver text scale. Session changes use a small listener `Set` in `src/services/api/sessionEvents.ts`; this is not a complete global auth store. The root also receives successful authentication directly by callback.

Shared components include Paper-based `TextField` and `Button`, dropdown/date controls, radio/checkbox controls, headers, refresh containers and image-source selectors. `AppAlertProvider` uses a modal, with cancel/destructive button styles and backdrop handling. It maintains one active alert, not a queue.

### Query state and local timeline state

[queryClient.ts](src/services/api/queryClient.ts) configures queries with one retry, 30-second stale time, five-minute garbage collection and refetch on reconnect/mount/focus. Mutations have zero automatic retries. Those defaults do not wrap direct `fetchGroupMessagesPageRequest()` calls made by custom hooks.

Query keys are `['groups', 'current-user']`, `['groups', groupId, 'messages']`, and an additional `infinite` suffix for the currently unused infinite-query hook. Create/send/update mutations update relevant cached data and invalidate queries; member/block/delete/prompt mutations generally invalidate the group list.

The active caregiver timeline uses **local `pagedMessages` plus local `liveMessages`**, not the unused infinite-query hook. ID-based merge helpers combine and sort them into dated, inverted list entries. Custom pagination tracks cursors and request IDs to reject stale results and repeated loads. It also writes results to Query's message cache. This hybrid design makes cache invalidation alone insufficient to guarantee that the visible local timeline refreshes.

### Authentication and persistence

- Auth responses support top-level or `data` envelopes. Login/register require access token, refresh token and user ID. The role normalizer recognizes receiver aliases and defaults other roles to caregiver.
- Login/signup persist the token bundle and full session in Keychain, and the profile in AsyncStorage, using `Promise.all`. This is concurrent storage, not a transaction.
- Keychain options request `AFTER_FIRST_UNLOCK_THIS_DEVICE_ONLY`, software security level and `AES_GCM_NO_AUTH`; no biometric prompt is requested by this configuration.
- Storage readers validate basic object shape, handle invalid JSON, and migrate legacy AsyncStorage auth entries to Keychain. Read/remove failures are logged and often converted to null/ignored; writes reject on failure.
- Startup requires token/session agreement and, when a profile exists, matching profile identity/role. It then calls `GET /api/v1/user/me` to synchronize role/profile. Server synchronization failure returns the previously stored session.
- Token expiry metadata is stored, but the request interceptor is reactive: it refreshes after an eligible HTTP 401, not before the recorded expiration time.
- Profile, push token, receiver text scale and Android camera-request history use AsyncStorage. No persistent Query cache or durable offline message outbox was found.

### Networking and token refresh

[httpClient.ts](src/services/api/httpClient.ts) creates Axios with a configurable base URL and 30-second timeout. Its async request interceptor reads Keychain tokens and attaches Bearer authorization when available.

[interceptors.ts](src/services/api/interceptors.ts) allows one retry of eligible 401 requests. Concurrent refreshes share `refreshTokensPromise`; refresh uses `{ refresh_token }`, normalizes snake/camel-case token fields, writes the token store and replays the original request. Auth-prefixed endpoints, refresh requests, explicitly skipped requests and already retried requests are excluded. A failed refresh calls `clearInvalidAuthState()`.

There are two important limitations: refresh does not synchronize the duplicate tokens in the stored session, and clearing invalid auth does not notify the root's session listener. These are source-based issues, detailed in section 14.

### Realtime lifecycle

[reverbClient.ts](src/services/realtime/reverbClient.ts) uses `pusher-js/react-native`, enables WS/WSS transports and authorizes private subscriptions with a Bearer-authenticated POST containing `socket_id` and `channel_name`. It expects `auth` in the response. This `fetch` path has no Axios refresh interceptor.

[ReverbProvider.tsx](src/providers/ReverbProvider.tsx) connects for authenticated sessions, watches group-query cache changes, subscribes to `private-group.<id>` by default and to `private-user.<id>`, and applies normalized events to message/group caches. It supports dotted and undotted event aliases for sent, acknowledged, seen-updated and user unread-count events. Exact server counts are applied when present; REST invalidation helps reconcile data.

`persistentSubscriptions.ts` wraps channels in a map with reference counts. Screen hooks bind their own handlers for local timeline state. Some cleanup paths unbind handlers without decrementing those reference counts, so the lifecycle is not fully balanced.

### Native images, typography and time

`cameraPermissionService.ts` uses Android permission APIs and a custom iOS Objective-C `CameraPermissionModule` backed by AVFoundation. Denied/restricted access can lead to an app-settings prompt. Android remembers a previous request and subsequently redirects to settings instead of requesting again.

`imageCropPicker.ts` requests 1024×1024 cropping, JPEG output and quality `0.85`, and maps the result to `{ uri, name, type }`. Picker cancellation returns null. Profile and group updates build multipart bodies.

`setupTypography.ts` patches `StyleSheet.create`, adds Lexend defaults to text components, maps weights to font families and removes `fontWeight` from transformed text styles. This global patch is an implementation choice with potential third-party UI effects, not just a theme object.

`dateTime.ts` formats most dates with `en-IN` and device-local timezone, and groups messages by local day. Invalid timestamps receive explicit fallbacks in several helpers. It does not set a fixed application timezone or normalize ambiguous backend date strings to UTC.

## 7. End-to-End Feature Workflows

### A. Sign in and resume a session

1. `AuthFormScreen` checks required fields and calls `useLoginMutation` with trimmed email and the entered password.
2. `authApi.loginRequest` posts credentials and normalizes the response; malformed token/user data rejects.
3. `persistAuthResponse` validates and stores tokens, session and profile, then starts device-token registration without blocking auth completion on push failure.
4. The form calls its parent callback; `RootNavigator` sets session state and renders caregiver or receiver tabs.
5. On a later launch, `restoreStoredAuthState` reads/validates the persisted bundle, attempts current-user synchronization, and restores a role branch or returns to auth.

Server password hashing, token issuance/revocation and role authorization cannot be inspected here.

### B. Create a caregiving or regular group

1. `GroupsStackNavigator` owns the draft shared across creation screens. Name/type lead to template selection for caregiving, or action definitions for a regular group.
2. `GroupMembersScreen` validates email inputs, discourages self-invitation and receiver/caregiver duplication, and collects prompt text/schedules where applicable.
3. `finishGroupCreation` maps UI templates to `custom`, `non_verbal`, `elder` or `safe`, converts schedules to minutes and constructs invite lists.
4. `createGroupRequest` sends JSON via `fetch`, caps custom signals at eight, filters invalid prompts, and manually refreshes/retries once on 401.
5. The returned group is mapped, prepended to the group cache and followed by list invalidation. Successful creation resets the draft and pops to the dashboard; errors show an alert.

Despite its name, `care_receiver_user_id` is populated with an **email** in this creation flow; the YAML create schema also declares email format. Email invitation delivery/claiming is a backend concern not demonstrated by the mobile code.

### C. Receiver sends a signal and sees activity

1. Receiver home selects the first group containing any member marked `isPrimary`, falling back to the first activity-sorted group. It does not explicitly match that primary member to the current user.
2. The mode determines the predefined legend; regular groups use nonempty backend label/color definitions.
3. A tap opens confirmation. `handleSendNow` guards missing group/pending state and posts `signal_slot` with `message_origin: 'manual'`.
4. `useSendGroupMessageMutation` deduplicates the response into the message cache, invalidates messages and updates the group preview.
5. Confirmation closes; home derives the latest five entries from queried messages. Failure closes confirmation and shows a normalized error.
6. A caregiver acknowledgement or read-related event can update cached activity through realtime. Reader lookup posts a numeric `message_id` and opens a sheet after a successful response.

The receiver send path is confirmation-first and updates after API success. The separate regular-group timeline send path is optimistic.

### D. Regular-group optimistic send, paging and acknowledgement

1. Group overview resolves group data from route, details and group cache. A custom pagination hook loads an initial page, with older pages loaded as the inverted list approaches history.
2. Sending a regular-group quick action inserts an `optimistic-<group>-<time>` message into local live state and requests scrolling to the latest entry.
3. API success replaces the optimistic ID with the returned message; failure removes it and shows an alert. ID merge helpers reconcile server echoes and page results.
4. Opening a focused group calls the mark-read endpoint, with an in-flight guard. Incoming messages can trigger another mark-read operation while focused.
5. Acknowledgement posts to the message-specific endpoint. Its mutation updates the Query cache; local visible timeline acknowledgement also relies on realtime events/local merging rather than subscribing directly to that cache.
6. A notification `messageId` triggers older-page loading until the message is found, then scrolls/highlights it. No end-to-end device test verifies this behavior.

### E. Admin edits members, prompts, signals or group photo

1. `useGroupAdminDetails` loads identity and group details; `canManageGroup` compares current user ID to `adminUserId`.
2. Member add/remove calls separate endpoints. Saved prompt edits use PUT; deletion uses a prompt-specific DELETE. Local unsaved prompts/signals can be removed without a remote call.
3. Custom signal saving computes changed slots and PATCHes them sequentially. Removing a saved signal sends null label/color. Partial completion is possible if a later slot fails.
4. A selected/cropped photo becomes multipart `group_photo`; successful update merges group details and adds a timestamp query parameter to refresh the image URL.
5. Exit, block, unblock and delete actions show confirmations. A receiver exiting a group can locally switch its stored role/profile to caregiver and emit the session-change event. Backend role rules remain unverified.

### F. Edit profile or delete account

Profile editing tracks changed name/photo, opens a source sheet, handles camera permission/cancellation, builds multipart data, patches the profile, merges/persists the result and returns to settings. A missing recognizable user payload falls back to submitted values; API success is therefore not always a server-profile confirmation.

Deletion is confirmed in shared settings, calls `DELETE /api/v1/users/me`, then invokes logout. Root logout attempts token unregistration and server logout in sequence, and clears local query/auth/profile state in `finally`. If unregistration rejects, server logout is skipped by that shared `try` block.

### G. Push notification open

After authentication, Firebase permission/token registration runs, with refresh and notification-open listeners. On a notification tap, `notificationHandler.ts` first attempts an explicit `link`, `url` or `deep_link`. Otherwise it resolves group ID, finds/fetches group details, and navigates to caregiver `Groups → groupOverview` if navigation is ready. There is no readiness queue or receiver-specific destination. Native delivery, APNs/FCM credentials and server message payloads were not exercised.

## 8. API Integration

Paths below omit all environment hosts and credentials. **Protected** means the client operation expects a signed-in session and uses the shared Bearer mechanism or explicit token header; actual server authorization is not verified. Public auth operations may still receive a stored Bearer header because the Axios request interceptor does not exclude them.

### Authentication and profile

| Method / path | Request and response handling | Auth / source |
| --- | --- | --- |
| POST `/api/v1/auth/register` | `{ full_name, email, password }`; normalized user/token envelope; confirmation is checked locally | Public auth flow; `src/features/auth/services/authApi.ts` |
| POST `/api/v1/auth/login` | `{ email, password }`; same normalized response and persistence | Public auth flow; same file |
| POST `/api/v1/auth/forgot-password` | `{ email }`; proceeds on successful HTTP response | Public recovery flow; same file |
| POST `/api/v1/auth/verify-reset-otp` | `{ email, otp }`; proceeds to reset, response message is not a retained proof | Public recovery flow; same file |
| POST `/api/v1/auth/reset-password` | `{ email, password, password_confirmation }`; successful response returns to sign-in | Public recovery flow; same file |
| POST `/api/v1/auth/refresh` | `{ refresh_token }`; accepts nested/top-level and snake/camel token names; persists new token pair | Refresh credential; `src/services/api/interceptors.ts` |
| POST `/api/v1/auth/logout` | Current hook sends default `{}`; service type also permits `push_token` | Protected; `authApi.ts` |
| GET `/api/v1/user/me` | Expects `data` or `user`; requires ID and recognized role; maps profile fields | Protected; `src/services/api/currentUser.ts` |
| PATCH `/api/v1/users/me` | Multipart `full_name`, `display_name`, `email`, `remove_photo`, optional `photo`; normalizes user or falls back to submitted profile | Protected; `src/features/shared/settings/services/profileApi.ts` |
| DELETE `/api/v1/users/me` | `fetch`, no body; one manual refresh/retry; empty success accepted, nonempty success must be JSON | Protected; same file |
| POST `/api/v1/users/me/device-tokens` | `{ platform, push_token }`; stores token locally after successful registration | Protected; `src/services/api/notificationsApi.ts` |
| DELETE `/api/v1/users/me/device-tokens` | JSON body `{ push_token }`; removes local token after success | Protected; same file |

### Groups and messages

All following calls are protected. Source: [groupsApi.ts](src/features/caregiver/groups/services/groupsApi.ts), [groupsEndpoints.ts](src/features/caregiver/groups/services/groupsEndpoints.ts) and the matching mutation/query hooks.

| Method / path | Request / response behavior |
| --- | --- |
| GET `/api/v1/groups` | Reads `data` or `groups` array, maps UI models; query sorts by latest activity |
| POST `/api/v1/groups` | JSON `group_name`, `group_type`, `group_mode`; optional email-valued `care_receiver_user_id`, `signals`, `prompts`, `invite_user_email`, `invite_user_role`; `fetch` with manual 401 retry; expects `data` or `group` |
| GET `/api/v1/groups/{groupId}` | Expects `data` or `group`; maps full details |
| PATCH `/api/v1/groups/{groupId}` | JSON when no photo; multipart when `group_photo` is present, including supported name/mode/receiver fields; maps updated group |
| DELETE `/api/v1/groups/{groupId}` | Deletes group; response body ignored |
| DELETE `/api/v1/groups/{groupId}/membership` | Exits current membership; body ignored |
| POST `/api/v1/groups/{groupId}/block` | Blocks membership; body ignored |
| POST `/api/v1/groups/{groupId}/unblock` | Unblocks membership; body ignored |
| PUT `/api/v1/groups/{groupId}/prompts` | `{ prompts: [{ prompt_text, frequency_minutes }] }`; returns `data` array or empty fallback |
| DELETE `/api/v1/groups/{groupId}/prompts/{promptId}` | Returns response object/message or empty fallback |
| POST `/api/v1/groups/{groupId}/members` | `{ email }`; requires membership `data` |
| DELETE `/api/v1/groups/{groupId}/members/{userId}` | Returns response/message or empty fallback |
| PATCH `/api/v1/groups/{groupId}/signals/{slot}` | `{ label, color }`, nullable for removal; expects updated group |
| GET `/api/v1/groups/{groupId}/messages` | Simple fetch expects `data` array. Paged fetch additionally handles nested pagination and cursor/page metadata |
| POST `/api/v1/groups/{groupId}/messages` | `{ signal_slot, message_origin?, prompt_id? }`; requires message in `data` |
| POST `/api/v1/groups/{groupId}/messages/{messageId}/acknowledge` | No request body; requires updated message in `data` |
| POST `/api/v1/groups/{groupId}/messages/read` | No body; requires `data` including group ID and unread count for cache update |
| POST `/api/v1/messages/readers` | `{ message_id: Number(messageId) }`; `data` array or empty fallback |

`groupsPagination.ts` accepts `nextCursor`/`next_cursor`, `prevCursor`/`prev_cursor`, `hasMore`/`has_more`, nested `data.data`, and numbered-page metadata or next-page URLs. Subsequent requests send `cursor`, plus numeric `page` when the cursor can be converted to a finite number. This is compatibility logic, not proof that every server variant works.

### Broadcast authorization and specification differences

The configured `REVERB_AUTH_ENDPOINT` receives a POST with `{ socket_id, channel_name }` and Bearer authorization. No environment URL is reproduced here. Successful JSON must contain `auth`; optional channel data/shared-secret fields are passed through to Pusher.

`api-docs.yaml` additionally describes `POST /api/v1/users`, invitation detail/accept/accept-existing routes and `POST /api/v1/groups/{groupId}/prompts`; no active frontend callers were found for those operations. Conversely, current-user GET, per-slot signal PATCH, group mark-read and message-reader routes used by the client are not represented in the inspected YAML path inventory.

The contract caps sent `signal_slot` at eight, while non-verbal UI definitions include slot nine. It also says not to send Bearer authorization on refresh, whereas the interceptor currently attaches any stored token. These discrepancies need backend confirmation.

### Errors and transport distinctions

Axios rejects failed HTTP responses and many service functions reject missing expected payloads. `getApiErrorMessage()` prioritizes backend messages/validation details, parses JSON error strings, and substitutes friendly timeout, connectivity, permission, rate-limit and server-error messages. This is not comprehensive sanitization of arbitrary backend text.

`fetch` callers explicitly check `response.ok`, parse response text and implement their own errors. Group creation and account deletion have no explicit fetch timeout/abort controller. They use the exported `API_BASE_URL`, so calling `setApiBaseUrl()` only changes Axios and does not reconfigure these paths or broadcast authorization.

## 9. Important Technical Decisions

| Observed choice | Evidence and supported explanation |
| --- | --- |
| Feature-based modules with services/hooks/screens | Repeated source structure and `src/CARELINK_STRUCTURE.md`; separates API access and rendering. Original team rationale is not recorded |
| Server cache plus local UI state | Query hooks and screen state; avoids placing transient forms/modals in query data. Why timeline paging is separately maintained is not documented |
| Shared auth form and settings navigator | Both roles/modes reuse components with props; directly reduces duplicate implementations. Historical motivation is unknown |
| Protected auth persistence with legacy migration | Keychain storage and migration code explicitly support old AsyncStorage records |
| Single-flight reactive refresh | Shared promise and `_retry` guard prevent concurrent refresh duplication and refresh loops in eligible Axios requests |
| Tolerant API normalization | Multiple envelope/field aliases are explicitly accepted. Whether this reflects API migration or inconsistent environments is unknown |
| Custom bottom-sheet adapter | `GroupBottomSheet.tsx` explicitly preserves the boolean-facing interface while replacing the Gorhom implementation; several legacy props are accepted but ignored |
| Build iOS React Native pods from source | `ios/Podfile` explicitly states this avoids missing-symbol linker issues with prebuilt artifacts |
| Always register device tokens | `notificationService.ts` comment explicitly identifies account switching and server-side resets as reasons |
| Global Lexend typography patch | Entry setup and theme code verify the behavior; why a global patch was chosen over explicit components is not documented |

## 10. Error Handling and Edge Cases

Implemented safeguards include required auth fields, password equality checks, numeric six-digit OTP input, invitation email validation, receiver/caregiver duplicate checks, custom-signal validation, disabled/pending action states, image-picker cancellation handling, corrupted-storage recovery and several mounted/request-ID guards.

Loading UI includes dashboard/home/timeline/detail skeletons, mutation activity indicators and pull-to-refresh flags. Empty lists and missing signal definitions have fallback rendering. Initial restoration uses `SplashScreen`, which currently returns an empty view.

Notable gaps:

- Auth forms do not implement a full email-format/password-strength policy; server validation remains necessary. Forgot-password checks only nonempty input. OTP errors collapse transport and validation failures into “invalid or expired.”
- Group list/home queries commonly default missing results to `[]` without a distinct rendered query-error state. An outage can look like no data.
- Timeline paging and activity reader failures are swallowed in several callbacks; users may get an empty/history-stalled view without retry guidance.
- A failed deep-link detail fetch can leave group overview on its loading screen because there is no explicit terminal error state.
- Settings pull-to-refresh rereads local profile storage; it does not fetch the server profile.
- Profile editing has no visible empty-name guard in `handleSave`; persistence fallback may hide a malformed successful response.
- Block confirmation calls the operation irreversible although an unblock action exists.
- Admin multi-slot saves are separate requests with no atomic rollback. Earlier slots may be saved when a later one fails.

## 11. Performance, Security, and Reliability

### Verified implementations

Performance-oriented code includes virtualized `FlatList` dashboards/history, inverted timeline batching (`initialNumToRender` and `maxToRenderPerBatch` of 16, `windowSize` 10), an activity list sized from viewport height, memoized derived values, ID-based message merging and page-load guards. The timeline preserves visible content and offers a latest-message jump instead of always moving a reader away from history. Images are cropped/compressed before upload. Shimmer animations use native-driver animation where implemented, and custom sheets use Reanimated shared values.

Security-related code includes Bearer requests, Keychain auth storage, legacy-token migration, disabled Android application backup, iOS transport security with arbitrary loads disabled, permission checks and destructive-action confirmations. These are specific controls, not evidence of a security audit or regulatory compliance.

Reliability measures include Query retries/refetch policies, network/focus integration, malformed-auth rejection, cache invalidation after many mutations and global exception/unhandled-rejection logging. The global handler delegates to any previous handler; it is not a React error boundary or crash-reporting service.

### Potential concerns and verification limits

- Token/session duplication and root notification gaps can affect authentication consistency; see prioritized findings below.
- Broadcast authorization does not refresh on 401. Persistent subscription references can accumulate until provider teardown, and per-event invalidations can amplify REST traffic.
- Message state exists in page state, live state and Query cache. Race conditions, stale acknowledgements and repeated refetches require integration tests.
- `logReverbEvent()` is currently a no-op in both development and production. Pusher console logging is enabled in development; other console warnings and the background push payload log remain present.
- `notificationHandler.ts` opens explicit notification URLs without a scheme/host allowlist. Treat payload trust and external URL handling as review items.
- Profile PII is in AsyncStorage. Local deletion wrappers can swallow failures. Keychain protection does not mean the whole app's data is encrypted.
- Android release uses debug signing and optional `releaseDebuggable`; minification is disabled. iOS APNs entitlement is `development`. Store readiness is unverified.
- The privacy manifest declares no collected-data types; its adequacy must be assessed against actual backend/profile/push behavior. No compliance conclusion can be drawn.
- No benchmark, load test, coverage target, crash-reporting integration, durable offline delivery or backend authorization test was found. No performance gain or delivery guarantee is claimed.

## 12. Setup and Execution

### Repository-derived prerequisites

- Node.js **>= 22.11.0**, as declared in `package.json`, and npm with the checked-in lockfile.
- Android development tools/emulator or device. Configuration specifies SDK/build tools 36, minimum SDK 24, NDK `27.1.12297006`, Kotlin `2.1.20`, Gradle wrapper `9.3.1`, and daemon JVM toolchain 17.
- For iOS: macOS, Xcode, Ruby/Bundler and CocoaPods. `Gemfile` requires Ruby >= 2.6.10, constrains CocoaPods and excludes particular dependency versions. Use `Gemfile.lock`; this review did not test every permitted Ruby version.
- The Xcode project records iOS deployment target 15.1; `Podfile` delegates to React Native's `min_ios_version_supported`. Confirm effective dependency/build compatibility rather than assuming those settings guarantee iOS 15.1 support.
- Access to an authorized API/Reverb environment and Firebase projects for device integration. No backend startup command exists in this repository.

### Install and run

These are setup instructions, not operations performed during this documentation task:

```bash
npm ci
bundle install
bundle exec pod install --project-directory=ios
npm start
```

Run the appropriate target in another terminal:

```bash
npm run android
# or, on macOS with iOS tooling:
npm run ios
```

`npm start` invokes `react-native start --reset-cache`. iOS native work uses `ios/expressme.xcworkspace` after pod installation. Android autolinks libraries, configures Vector Icons fonts and conditionally applies the Firebase Google Services plugin when its file exists. `MainApplication.kt` creates a high-importance vibrating default notification channel on supported Android versions.

### Environment configuration: important limitation

The API/realtime config modules read `process.env`; `.env` exists locally and is ignored by Git. **No `.env` loader or environment-injection configuration was found in package dependencies, Babel or Metro. Creating `.env` alone is therefore not a verified way to configure a native bundle.** An external build system might inject these values, but none is present here. Confirm the effective bundle values and approved injection mechanism before connecting to an environment. Existing source defaults target an external environment and are deliberately omitted from this document.

| Variable | Use |
| --- | --- |
| `API_BASE_URL` | REST base, including URL construction in explicit fetch services |
| `REVERB_APP_KEY` | Client broadcast app identifier; do not substitute a server secret |
| `REVERB_HOST` | WebSocket host |
| `REVERB_PORT` | Numeric port; source fallback is 443 |
| `REVERB_SCHEME` | TLS enabled only when value equals `https` |
| `REVERB_AUTH_ENDPOINT` | Full private-channel authorization endpoint |
| `REVERB_CHANNEL_PREFIX` | Default group prefix `group.` |
| `REVERB_CLUSTER` | Pusher-compatible cluster option |

Firebase files are present at `android/app/google-services.json` and `ios/expressme/GoogleService-Info.plist`; their values are not reproduced or validated against external projects. iOS conditionally initializes Firebase from its bundled plist and declares remote-notification background mode. Do not assume conditional native setup makes JS Firebase calls safe when configuration is missing: `index.js` initializes messaging unconditionally.

### Commands and build limits

| Command | Purpose |
| --- | --- |
| `npm start` | Metro with reset cache |
| `npm run android` | React Native Android run command |
| `npm run ios` | React Native iOS run command |
| `npm run lint` | ESLint across the project |
| `npm test -- --runInBand --watch=false` | Existing Jest suites |
| `./node_modules/.bin/tsc --noEmit --incremental false` | Type-check without emitted files; no dedicated npm typecheck script |
| `node scripts/find-unused-styles.js` | Heuristic unused-style inspection, not a correctness test |
| `cd android` then `./gradlew assembleRelease` or `./gradlew bundleRelease` | Standard release tasks derived from the Android application configuration; not executed here |

The style script uses regex-based `styles.KEY` matching across files and a transitive `glob` dependency, so its findings are candidates, not safe automatic deletions. No dedicated release npm script, CI workflow, Fastlane pipeline or deployment automation was found. Existing release metadata/baseline profiles do not prove a reproducible current build or store deployment. Android release signing must be configured appropriately before distribution.

## 13. Testing and Quality

Checks run successfully against the current working tree during this review:

| Check | Result |
| --- | --- |
| `npm test -- --runInBand --watch=false --cacheDirectory=/private/tmp/expressme-analysis-jest` | **2 suites, 7 tests passed**, no snapshots |
| `npm run lint -- --no-cache` | Passed, exit code 0 |
| `./node_modules/.bin/tsc --noEmit --incremental false` | Passed, exit code 0 |

[App.test.tsx](__tests__/App.test.tsx) mounts the app with `RootNavigator` mocked out; it does not exercise session restoration, role screens, API calls or native notification behavior. [errorMessage.test.ts](__tests__/errorMessage.test.ts) has six tests for validation messages, generic 404 replacement, network failures, timeouts, JSON error strings and hiding internal response-shape errors.

`jest.config.js` uses the React Native preset, disables Watchman and configures transforms; `jest.setup.js` supplies Gesture Handler, Reanimated, Safe Area and NetInfo mocks. ESLint extends the React Native config; Prettier uses single quotes, trailing commas and omitted single-argument arrow parentheses. TypeScript extends the React Native config and excludes dependency/Pods directories.

These checks validate the existing baseline only. No new tests or source fixes were made. No device/emulator run, native build, backend request, notification delivery test, WebSocket session, account mutation or visual review was performed.

## 14. Challenges and Improvement Opportunities

The following are concrete source-based findings, not claims of observed production failures. Priorities reflect likely impact, not a measured incident history.

| Priority | Finding, impact and proposed follow-up | Evidence |
| --- | --- | --- |
| High | **Refreshed tokens diverge from stored session.** Refresh writes only the token store; startup insists its access/refresh strings equal the session copies. A later launch can clear otherwise valid auth. Use one authoritative token bundle or synchronize both stores; test refresh → relaunch | `src/services/api/interceptors.ts`, `authRecovery.ts`, `sessionStorage.ts` |
| High | **Invalid-auth cleanup does not update root session state.** `clearInvalidAuthState` clears storage/cache but never calls `notifyAuthSessionChanged(null)`. Mounted authenticated UI can remain after refresh failure. Centralize and test the transition | Same files plus `sessionEvents.ts`, `src/navigation/RootNavigator.tsx` |
| High | **Startup can restore stale auth after server sync failure.** `syncAuthStateWithServer` returns the original session for all failures, including an auth failure that may already have cleared storage. Distinguish temporary offline failure from invalid credentials | `src/services/api/authRecovery.ts` |
| High | **Password-reset authorization is unverifiable.** Reset sends email/password only after a separate OTP request; no OTP/proof is retained by the frontend. Verify server binding, expiry and replay protection; do not assert this is insecure without server evidence | Auth recovery screens/types and `api-docs.yaml` |
| High | **Environment injection is unresolved.** Visible build tooling does not load `.env`; external fallback endpoints can be used unintentionally. Make configuration deterministic and validate it before release | API/realtime `config.ts`, Babel/Metro/package configuration |
| High | **Push destination/deep-link gaps.** Native link registration is absent; handler targets a caregiver tab even for receivers and discards navigation if not ready. Add role-aware deferred navigation and test cold/warm starts | `src/navigation/notificationHandler.ts`, `src/App.tsx`, native manifest/plist |
| Medium | **Subscription reference counts are unbalanced.** Screen cleanup and group removal often unbind without `persistentUnsubscribe`; repeated opens retain counts/channels. Balance acquisition/release and test navigation/group removal | `persistentSubscriptions.ts`, `ReverbProvider.tsx`, `useGroupRealtimeTimeline.ts`, receiver activity |
| Medium | **Visible acknowledgement can depend on WebSocket delivery.** Mutation success updates Query cache, while active timeline renders local arrays. Without an event, the acknowledged result may not appear locally. Reconcile the returned message directly or consolidate state | `GroupOverviewScreen.tsx`, `useAcknowledgeGroupMessageMutation.ts` |
| Medium | **Pagination cache write depends on state-updater timing.** Appending initializes `nextMessages = []`, assigns it inside `setPagedMessages`, then immediately writes it to cache. React need not run the updater synchronously. Compute/state-sync without that assumption | `useGroupMessagePagination.ts` |
| Medium | **Local paging bypasses Query resilience.** Invalidating message queries does not refresh local pages; errors are often swallowed. Consolidate pagination or add explicit retries/error states/reconnect handling | Timeline pagination and receiver activity |
| Medium | **Receiver group choice is broad.** It chooses a group with any primary member, not explicitly the current receiver. Confirm the single-group product rule or add identity-based selection | Receiver home/activity |
| Medium | **Signal contract mismatch.** Non-verbal slot nine exceeds YAML message maximum eight; confirm backend support and align the contract | `groupSignalDefinitions.ts`, `api-docs.yaml` |
| Medium | **Boolean/identity normalization needs stricter tests.** `resolveIsBlocked` treats every string except `no` as true, including `false` and `0`; missing member IDs can become `NaN` strings. Validate expected payload schemas | `groupsMappers.ts` |
| Medium | **Empty updated arrays can resurrect old data.** `resolveGroupSnapshot` uses `.length` fallbacks for members/prompts/signals; a legitimate empty array can fall back to a stale route snapshot | `GroupOverviewScreen.helpers.ts` |
| Medium | **REST clients have differing behavior.** Explicit fetch requests bypass timeout and common interceptors; changing Axios base URL does not change fetch targets. Unify request construction/error/refresh policies | `groupsApi.ts`, `profileApi.ts`, `httpClient.ts`, `reverbClient.ts` |
| Medium | **Logout may skip remote revocation.** Unregistration failure skips the following logout request, although local cleanup runs. Attempt independent cleanup actions and report relevant failures | `RootNavigator.tsx` |
| Medium | **Profile/source mismatch can look successful.** Profile update falls back to input when response shape is unexpected; settings refresh only reads local storage. Add authoritative fetch/validation | Profile service/hooks and shared settings navigator |
| Medium | **Native release configuration is development-oriented.** Replace debug signing, verify release debuggability/APNs entitlements, effective minimum iOS version and privacy declarations | Android build and iOS project/entitlements/privacy files |
| Low | **Custom sheet compatibility props are misleading.** Legacy snap/keyboard props are accepted but ignored; callers may expect behavior they do not receive. Narrow or document the API | `GroupBottomSheet.tsx` |
| Low | **Text-scale persistence rounds inconsistently.** Bounds include 0.85/1.35 while clamping/persistence round to one decimal; test behavior at limits and across restart | `ReceiverTextScaleProvider.tsx` |
| Low | **Maintenance/documentation debt.** Unconnected screens/hooks, no-op sharing and header-only policy pages can overstate scope; old README paths point to another local checkout | Existing READMEs, navigator imports and settings screens |

Recommended test order: auth refresh/relaunch/failed restore; simultaneous 401s; logout cleanup failures; REST/realtime races and duplicate messages; paging across group switches; notification taps for both roles; camera denial/cancellation; multi-slot partial saves; malformed API data. Device tests should include both platforms and denied push permissions.

## 15. Interview Preparation

Use these answer outlines to explain the actual implementation and its tradeoffs. Do not present recommendations as work already completed.

| Question | Brief answer outline grounded in this code |
| --- | --- |
| What problem does the app solve? | Signal-based group communication for caregivers/receivers; receiver home reduces typing, caregiver timeline provides shared visibility. No verified business metrics |
| Walk through app startup | `index.js` → typography/FCM registration → app providers → root restoration → current-user sync → role tabs |
| Why separate service, hook and screen? | Services map transport data, mutation/query hooks coordinate server cache, screens own user interaction. Cite send-group-message; acknowledge direct-service exceptions |
| How are roles determined? | Backend auth/current-user response becomes `careGiver`/`careReceiver`; root chooses tabs. Signup has no role selector. Exiting a receiver group locally changes role; backend rules unknown |
| How do screen components communicate? | Callback props and parent-owned drafts; contexts for shared services; Query cache for server data; auth listener set for explicit session changes |
| Why use refs as well as state? | State renders loaders/messages; refs hold in-flight cursor, request ID, latest callback and focus values needed by async handlers without extra rendering |
| Explain stale closures in this project | WebSocket/keyboard listeners outlive renders; current callback/focus refs prevent handlers from using stale values. Stable dependencies and cleanup remain necessary |
| How does concurrent token refresh work? | Single shared promise, skip conditions, `_retry` marker, write new token pair, replay each eligible request. Explain unsynchronized session copies and missing root-clear event |
| Where are credentials stored? | Keychain with explicit accessibility/security/storage options; legacy AsyncStorage migration. Profile and preferences stay in AsyncStorage; no biometric requirement |
| Is this app offline-first? | No durable offline outbox/cache. NetInfo controls Query online state and a banner; direct fetch paths differ. Do not claim guaranteed delivery after restart |
| Why can a successful acknowledgement remain visually stale? | Mutation updates Query cache, but overview renders local paged/live arrays; success or realtime result must reach those arrays |
| How do optimistic messages avoid duplicates? | Temporary ID before request, replace/remove on success/failure, real-ID merging across pages/live events. Timing of echoes and malformed IDs deserves tests |
| How does pagination work? | Normalize cursor/page envelopes, track loaded/in-flight cursors, reject stale request IDs, merge by ID and render inverted timeline. Infinite-query hook exists but is unused |
| How would you debug incorrect unread counts? | Separate backend group count, UI “new while scrolled away” count, acknowledgements and read receipts; inspect exact-count events, focus-triggered mark-read and REST reconciliation |
| What happens to WebSockets when navigating? | Provider owns client, persistent map shares channels, screens bind local handlers; current unbind-only cleanup leaves reference-count gaps |
| What is the difference between push and realtime here? | WebSockets update active app data; Firebase registers device tokens and handles opens. Foreground callback shows no alert and background JS only logs |
| Why can a notification tap fail? | Missing native link registration, navigation not ready, wrong role tab, invalid/missing group data, failed detail fetch or authorization |
| How are image uploads implemented? | Platform permission service → camera/library crop → `{uri,name,type}` → multipart profile/group PATCH. Cancellation returns null; custom Objective-C module supplies iOS permission state |
| What React Native native work exists? | Kotlin app host/default notification channel, Swift app/Firebase bootstrap, Objective-C AVFoundation permission bridge; autolinking and native manifests |
| Why use FlatList and an inverted timeline? | Virtualization bounds rendered rows; inversion keeps latest at offset zero, with history paging and visible-position preservation. Cite configured batch/window values, not invented performance gains |
| Explain a JavaScript async hazard you found | `Promise.all` persistence is nontransactional; state-updater execution is not a safe synchronous place to assign a variable used immediately afterward; logout sequence skips later work on rejection |
| What is unusual about typography? | `StyleSheet.create` is patched globally to map Lexend families and remove weight; discuss third-party impact and testing rather than calling it ordinary theme inheritance |
| Are prompts scheduled by the phone? | UI configures frequency/text via API; no scheduler worker here. Actual execution and push delivery are external |
| How would you improve error handling? | Preserve friendly normalization, add distinct query/paging error states, avoid swallowing failures, distinguish auth invalidity from offline restoration and validate response shapes |
| What do the tests prove? | Mocked app shell renders and six error-normalizer scenarios work; no auth integration/native/realtime tests. Lint and TypeScript passed but do not prove device correctness |
| What would you fix first? | Token/session/root consistency, explicit environment configuration, then role-aware push/link routing and timeline/cache integration tests |

## 16. Resume-Relevant Contributions

The repository supports the following **demonstrable engineering topics**. Use a bullet only if it accurately reflects your own work; source presence alone does not establish authorship, leadership or ownership.

- Developed role-based React Native interfaces for caregiver group management and receiver signal communication using TypeScript and React Navigation.
- Integrated REST authentication, profile and group APIs with Axios, TanStack Query mutation/cache handling and native secure token storage.
- Implemented signal timelines with paginated history, optimistic regular-group sending, acknowledgement actions and Reverb-compatible realtime updates.
- Built multi-step group forms with custom signal colors, email validation, recurring prompt configuration and member administration.
- Integrated Firebase device-token registration and notification-open routing, with Android notification-channel configuration.
- Added camera/library image cropping and multipart profile/group photo uploads, including platform-specific permission handling and an iOS native bridge.
- Created reusable UI controls, animated custom bottom sheets, offline feedback and persisted receiver text scaling.
- Added or maintained API error normalization and Jest tests, if those changes are attributable to you.

A defensible compact project description is: **“ExpressU: a TypeScript React Native care-communication app with role-based navigation, group signals, REST integration, realtime timelines, push integration and native image uploads.”** Qualify incomplete notification/link behavior when discussing implementation depth.

Do not claim backend implementation, database design, production deployment, medical certification, complete accessibility compliance, measured speed improvements, user counts, revenue impact or sole ownership from this evidence. For interviews, prepare an actual change/diff you authored and explain its validation and remaining limitations.

## 17. Knowledge Gaps to Address

| Priority | Study topic | Why it matters here / practical exercise |
| --- | --- | --- |
| 1 | Auth state machines, token rotation and secure persistence | Explain one authoritative auth source, atomicity, failed refresh vs offline restore; design tests for refresh → restart and concurrent 401s |
| 1 | React state scheduling, effects, refs and closures | Reproduce the pagination updater/cache timing issue; trace listener setup/cleanup and async results after navigation |
| 1 | TanStack Query cache semantics | Understand query keys, stale/GC time, invalidation, mutation callbacks and the difference between cached state and local message arrays |
| 1 | REST contracts and runtime validation | Explain snake/camel mapping, nullable fields, malformed payloads, role normalization and the slot-nine/spec mismatch |
| 1 | WebSocket delivery and reconciliation | Study duplicates, ordering, disconnect/reconnect, private-channel auth and reference-count cleanup; model REST response/event races |
| 2 | React Navigation and native deep links | Contrast native stacks with local route state; implement readiness queues, role-aware destinations and native registration in a future change |
| 2 | Push lifecycle on Android/iOS | Distinguish foreground/background/killed states, permissions, Android channels, APNs entitlements and token/account association |
| 2 | Native modules and build compatibility | Understand the Objective-C bridge, autolinking, new architecture, Gradle/JDK/SDK versions, CocoaPods and effective deployment targets |
| 2 | Image permissions and multipart networking | Test denial, settings return, picker cancellation, URI/MIME handling and PATCH multipart server expectations |
| 2 | Mobile security and release configuration | Review environment injection, server-side authorization, URL trust, log redaction, signing and persistence failure handling |
| 2 | Testing asynchronous integration | Add deterministic mocks/fake events for auth, paging, optimistic rollback and subscriptions; complement them with device journeys |
| 3 | Accessibility and rendering performance | Test receiver scaling with system font settings/screen readers; profile real lists before claiming optimizations |
| 3 | Date/time contracts | Require unambiguous backend timezone data; test invalid timestamps, midnight grouping and device-timezone changes |

### Review coverage and unverified areas

The review inspected entry points, application/navigation/provider code, auth/group/receiver/settings implementations, service/hook/type/helper layers, reusable UI and typography, native Android/iOS configuration and custom module, package/build/test configuration, API contract, existing documentation and the style-inspection script. Dependency lockfiles were checked for relevant versions, not audited as third-party source.

Existing working-tree edits were included as current evidence: `Agent.md`, app/error tests and Jest setup, password recovery, receiver home, API error handling and the iOS lockfile; a pre-existing deleted release APK was not restored. No existing application, dependency, configuration or documentation file was changed for this task.

No relevant first-party source was inaccessible. Installed dependencies, generated Pods/build outputs, Git internals, IDE state, binary fonts/images/keystores and release binaries were excluded from source-level auditing; assets were inventoried and their imports traced rather than visually evaluated. Environment values, Firebase credential/configuration values and external hostnames were intentionally not reproduced. Backend source, live API behavior, production configuration, server authorization/scheduling, real push/WebSocket delivery, device UI behavior and deployment history remain unverified.
