# E-Learning Project — Repository Analysis

Source-based reference for maintaining the application, preparing for interviews, and identifying defensible resume contributions. Reviewed on 23 October 2026. The existing root `README.md` is preserved.

**Evidence boundary:** “Implemented” means present in the inspected source, not proven in a running deployment. Both frontend and backend are included. No live database, payment, media, browser, or deployment verification was performed. Recommendations and potential failure modes are identified separately from implemented behavior. Dependency versions below are declared package ranges, not claims about installed software.

## 1. Project Overview

This is a browser-based learning management application built with MongoDB, Express, React, and Node.js. It connects instructors who create and sell video courses with students who discover courses, purchase access, and record learning progress.

The implemented scope includes account registration and login, student/instructor navigation, a published course catalog, search and filters, course and lecture authoring, media uploads, Stripe Checkout, enrollment, lecture progress, profiles, and an instructor sales view.

This is **React web, not React Native**. There are no Android/iOS projects, native modules, Expo configuration, or mobile application builds. Mobile support is responsive browser styling. No implementation was found for certificates, quizzes, assignments, ratings, password reset, email verification, live classes, offline playback, refunds, or instructor payouts. Footer “About” and “Contact” text does not establish corresponding pages.

## 2. Technology Stack

| Technology | Declared version/range | Verified usage |
| --- | --- | --- |
| JavaScript / JSX | ES modules | Both packages use `type: module`; React components use JSX. No TypeScript application source. |
| React / React DOM | ^18.3.1 | Components, hooks, context, browser root, StrictMode, lazy routes. |
| Vite / React plugin | ^5.4.10 / ^4.3.3 | Frontend development and build; `@` alias to `src`. |
| React Router DOM | ^6.28.0 | Browser router, nested layouts, route parameters, search parameters, redirects. |
| Redux Toolkit / React Redux | ^2.3.0 / ^9.1.2 | Auth state and four RTK Query API caches/middleware. |
| Tailwind CSS / PostCSS / Autoprefixer | ^3.4.14 / ^8.4.47 / ^10.4.20 | Responsive utilities, CSS variable colors, class-based dark theme. |
| Radix UI / shadcn-style components | Multiple Radix 1.x/2.x packages | Local reusable primitives for dialogs, sheets, selects, tabs, menus, controls, avatars. `components.json` records shadcn configuration. |
| CVA, clsx, tailwind-merge | ^0.7.0, ^2.1.1, ^2.5.4 | Component variants and conditional class merging via `cn`. |
| Lucide / Sonner | ^0.455.0 / ^1.7.0 | Icons and mutation feedback toasts. |
| React Quill | ^2.0.0 | Rich HTML course description editing. |
| React Player | ^2.16.0 | Course detail preview; the learning page instead uses native HTML `<video>`. |
| Axios | ^1.7.7 | Video upload and upload progress events; other requests use RTK Query `fetchBaseQuery`. No Axios interceptors found. |
| Recharts | ^2.13.3 | Instructor sales line chart and responsive container. |
| next-themes | ^0.4.3 | Imported by the toast wrapper; main app uses its own theme context instead. |
| Express | ^4.21.1 | REST-style routing and middleware. |
| Mongoose | ^8.8.0 | MongoDB schemas, references, population, CRUD operations. |
| bcryptjs / jsonwebtoken | ^2.4.3 / ^9.0.2 | Password hashing/comparison and signed login tokens. |
| cookie-parser / cors / dotenv | ^1.4.7 / ^2.8.5 / ^16.4.5 | Cookie parsing, origin policy, server environment loading. |
| Multer / Cloudinary | ^1.4.5-lts.1 / ^2.5.1 | Disk-backed multipart upload, remote media upload and deletion. |
| Stripe | ^17.3.1 | Hosted checkout sessions, session verification, signed webhook handling. |
| ESLint / Nodemon | ^9.13.0 / ^3.1.7 | Frontend lint configuration and backend development reload. |

`client/package.json` also declares `server: file:..`. In this checkout, that points at the repository root, which has no `package.json`; the backend is actually in `server/`. The lockfile's parent-package entry reflects a different layout. Treat installation as unverified until this inconsistency is resolved separately.

## 3. Architecture and Folder Structure

```text
e-learning/
├── README.md                         Existing documentation, preserved
├── PROJECT_ANALYSIS.md               This source-based analysis
├── client/
│   ├── index.html                    Browser entry and root element
│   ├── package.json, package-lock.json
│   ├── .env.example, .gitignore
│   ├── vite.config.js, jsconfig.json  Build/editor aliases
│   ├── tailwind.config.js, postcss.config.js
│   ├── eslint.config.js, components.json, vercel.json
│   ├── public/vite.svg                Template favicon
│   └── src/
│       ├── main.jsx, App.jsx         Providers, bootstrap, routes
│       ├── index.css, App.css        Theme tokens; App.css is empty
│       ├── app/                      Store and rootRedcuer.js
│       ├── features/authSlice.js     Session identity/readiness
│       ├── features/courseSlice.js   Empty file, not a course state implementation
│       ├── features/api/             Auth, course, purchase, progress APIs
│       ├── layout/MainLayout.jsx     Navbar, Outlet, footer
│       ├── components/               Guards, editor, purchase button, theme
│       ├── components/ui/            Reusable UI primitives
│       ├── pages/Login.jsx           Login/signup tabs
│       ├── pages/student/            Discovery, profile, learning screens
│       ├── pages/admin/              Instructor layout and dashboard
│       ├── pages/admin/course/       Course management
│       ├── pages/admin/lecture/      Lecture management
│       ├── lib/                      API base resolver and class helper
│       ├── DarkMode.jsx              Theme selector
│       └── assets/react.svg          Template asset
└── server/
    ├── index.js                      Express startup and global middleware
    ├── package.json, package-lock.json, vercel.json
    ├── .env.example, .gitignore
    ├── database/db.js                MongoDB connection
    ├── routes/                      Five API route groups
    ├── controllers/                 User, course, purchase, progress logic
    ├── models/                      User, Course, Lecture, Purchase, Progress
    ├── middlewares/isAuthenticated.js
    ├── utils/                       JWT, cookies, Cloudinary, Multer
    └── scripts/smoke.mjs             Five HTTP smoke checks
```

The frontend communicates with Express over HTTP. Express controllers use Mongoose for metadata and relationships, Cloudinary for media, and Stripe for payment processing. Components do not connect directly to MongoDB or Stripe's secret-key API. There is no separate service/repository layer, queue, or websocket server.

The request pipeline in `server/index.js` is CORS → selective JSON parsing → cookie parsing → response headers → in-memory rate limiting → routers. The webhook path skips JSON parsing and rate limiting; its router consumes the raw body for signature verification. Startup calls `connectDB()` without awaiting it before listening.

## 4. Features and Modules

| Module | Implemented behavior | Primary implementation |
| --- | --- | --- |
| Accounts | Signup with student/instructor role, immediate login, password login, logout, profile bootstrap | `client/src/pages/Login.jsx`, `features/api/authApi.js`, `server/controllers/user.controller.js` |
| Catalog | Public published courses, thumbnail, creator, level, price, loading skeletons | `client/src/pages/student/Courses.jsx`, `Course.jsx`; `getPublishedCourse` |
| Search | Keyword over title/subtitle/category, category selection, ascending/descending price | `HeroSection.jsx`, `SearchPage.jsx`, `Filter.jsx`, `SearchResult.jsx`; `searchCourse` |
| Course detail | HTML description, curriculum, enrollment count, available preview, buy/continue control | `client/src/pages/student/CourseDetail.jsx`; `getCourseDetailWithPurchaseStatus` |
| Course authoring | Create draft, edit metadata/thumbnail, publish/unpublish, delete | `client/src/pages/admin/course/`; `server/controllers/course.controller.js` |
| Lectures | Create title, upload video, save metadata/free-preview flag, edit/delete | `client/src/pages/admin/lecture/`; course controller and media router |
| Checkout/enrollment | Hosted INR checkout, signed webhook, return-page verification, enrollment references | `BuyCourseButton.jsx`, `PurchaseCourseProtectedRoute.jsx`, purchase API/controller |
| Learning | Enrolled courses, video selection/playback, viewed markers, complete/incomplete toggle | `MyLearning.jsx`, `CourseProgress.jsx`; progress API/controller |
| Profile | User details, enrolled courses, name/photo update dialog | `Profile.jsx`; user controller |
| Instructor reporting | Completed sales count, summed purchase amounts, one chart point per sale | `client/src/pages/admin/Dashboard.jsx`; `getAllPurchasedCourse` |
| Shared UX | Desktop/mobile navigation, responsive layout, light/dark/system theme, toasts | `Navbar.jsx`, `Sidebar.jsx`, `ThemeProvider.jsx`, `DarkMode.jsx`, `components/ui/` |

Sales are not grouped into a time series or aggregated per course: repeated purchases of the same course produce repeated title labels. Revenue is a sum of completed purchase amounts, not profit or net settlement. The role named “admin” in file paths is the instructor role; there is no separate administrator role in the user schema.

## 5. Application Flow

All routes are defined in `client/src/App.jsx` and share `MainLayout`.

| Browser path | Screen / access behavior |
| --- | --- |
| `/` | Public hero and published course catalog |
| `/login` | Signup/login; authenticated visitors redirect home |
| `/my-learning` | Authenticated user's enrolled course cards |
| `/profile` | Authenticated user's profile and editing dialog |
| `/course/search?query=...` | Authenticated search and filter screen |
| `/course-detail/:courseId` | Authenticated detail with purchase status |
| `/course-progress/:courseId` | Authentication guard plus frontend purchase guard |
| `/admin/dashboard` | Instructor sales view |
| `/admin/course` | Instructor's course table |
| `/admin/course/create` | New draft course |
| `/admin/course/:courseId` | Course editing |
| `/admin/course/:courseId/lecture` | Lecture creation/list |
| `/admin/course/:courseId/lecture/:lectureId` | Lecture editing |

`ProtectedRoute` waits for `authChecked`, then redirects unauthenticated users to login. `AdminRoute` additionally checks `user.role === "instructor"`. These are browser navigation controls; backend authorization is a separate concern. No return-to-original-page state is preserved by the login redirect. `/admin` has no index child, and no explicit custom catch-all/error route is configured.

Student journey: home → login/signup when required → search or course detail → Stripe hosted checkout → progress URL containing `session_id` → purchase verification → lectures. Later, My Learning → course detail → Continue Course returns to the player. Playback position and last-selected lecture are not persisted; selection starts from the first lecture on a fresh mount.

Instructor journey: register/select instructor → account menu dashboard → Courses → Create → course table → edit details → lectures → upload/save lecture → publish. Mobile instructor navigation uses buttons above the content instead of the desktop sidebar.

## 6. Technical Implementation

### Components, state, hooks, and communication

`main.jsx` creates the React DOM root under StrictMode and Redux `Provider`. `App` initiates `useLoadUserQuery`, supplies the custom theme provider, and renders the router. Page components and layouts are dynamically imported and wrapped in Suspense with `LoadingSpinner`.

`app/store.js` registers all four RTK Query middleware instances. `app/rootRedcuer.js` combines their reducers with `authSlice`. Auth state holds `user`, `isAuthenticated`, and `authChecked`; it is not persisted to localStorage. The HttpOnly cookie restores the session through `/user/profile` after a page reload.

Forms, selected filters, file previews, upload state, and the current lecture use `useState`. `useEffect` hydrates forms from fetched records and responds to mutation success/errors with toasts, navigation, and refetches. Router hooks read IDs/search parameters and navigate. `useSelector` supplies identity to guards/navbar. The custom `useTheme` wraps context.

Examples of parent-child communication: `Course` receives a `course` prop; `Filter` reports selections through `handleFilterChange`; `RichTextEditor` receives `input` and `setInput` and updates the description field. UI primitives forward refs and props, compose Radix controls, and merge styles with `cn`. No dedicated custom business-hook layer is present.

### Server state and asynchronous work

The four `features/api/*.js` modules expose generated query/mutation hooks. All use `credentials: "include"`. Course creation/editing invalidate `Refetch_Creator_Course`; lecture removal invalidates `Refetch_Lecture`. Several screens manually call `refetch` after changes. Invalidation is incomplete: course removal invalidates lecture tags rather than the creator-course list, and publishing does not invalidate catalog queries.

Awaiting an RTK Query mutation without `.unwrap()` yields its result object; rejection handling in `try/catch` must account for that. Course deletion and payment verification use `.unwrap()`, while many other actions rely on hook status flags. There are no general optimistic cache patches. Logout immediately clears auth state without awaiting `queryFulfilled`, and does not reset API caches.

Axios is used for multipart video uploads because that call wires `onUploadProgress`. The progress indicator measures upload transfer to the API, not the subsequent Cloudinary processing time.

### Persistence model

| Model/file under `server/models/` | Fields and relationships |
| --- | --- |
| `user.model.js` | Name, email, hashed password, role enum, photo URL, enrolled course ObjectId references; timestamps. Email has no unique index in the schema. |
| `course.model.js` | Title/category required; subtitle, description HTML, level enum, price, thumbnail, creator, lecture references, enrolled students, `isPublished` default false; timestamps. |
| `lecture.model.js` | Required title, video URL, Cloudinary public ID, preview flag; timestamps. Course membership is held on Course. |
| `coursePurchase.model.js` | Required course/user references, amount, payment ID, pending/completed/failed status; timestamps. Payment ID contains a Checkout Session ID. No unique payment index. |
| `courseProgress.js` | String user/course IDs, completed flag, embedded `{lectureId, viewed}` entries. No timestamps, compound unique index, or declared course reference. |

Course, lecture, purchase, and user operations use separate writes, without transactions. Enrollment is duplicated across User and Course, so consistency depends on finalization completing both updates. The progress controller calls `.populate("courseId")` despite that field having no reference; it separately fetches Course for actual course details.

### Authentication and files

Registration checks required fields and existing email, hashes passwords with bcrypt cost 10, creates the user, and issues a JWT. Login compares the hash. JWT payload contains `userId`, expires after one day, and is stored in `token` with HttpOnly enabled. Cookie lifetime is seven days; production defaults are Secure and SameSite=None, development defaults are non-Secure and Lax. Environment values can override these settings.

Profile responses and login/signup payloads exclude passwords. Course detail creator population does not apply the same exclusion (see security findings).

Multer writes uploads to relative `uploads/`; Cloudinary uses `resource_type: "auto"`. Images use `secure_url`; the lecture upload component saves the response's `url` and `public_id`. No temporary-file removal, file-size limits, or server MIME/type validation is configured.

## 7. End-to-End Feature Workflows

### A. Signup and session restoration

1. `Login.jsx` maintains name/email/password/role input and calls the signup mutation.
2. `POST /user/register` validates presence, checks duplicate email, hashes the password, and persists User.
3. `generateToken` sets the cookie and returns a safe user object; `authApi.onQueryStarted` dispatches `userLoggedIn`.
4. The authenticated-login guard redirects home. On reload, `App` fetches the profile, which populates enrolled courses and repopulates auth state.
5. Missing-cookie requests receive 401; thrown JWT verification errors currently only log, leaving a response-handling gap.

### B. Discover and preview a course

1. `HeroSection` navigates to a search URL. `SearchPage` passes query/categories/sort into `useGetSearchCourseQuery`.
2. The API builder URL-encodes arguments; the backend combines `isPublished: true`, case-insensitive regex matching, optional category `$in`, and price sorting.
3. Search results link to course detail. Its purchase API populates creator/lectures and looks for a completed purchase by this user.
4. Non-preview video URLs are blanked for non-purchasers. The UI plays the first returned lecture with a URL; it does not offer selection among all free previews.
5. Catalog access is public, but search/detail APIs and their screens require login.

### C. Author and publish a course

1. `AddCourse` submits title/category; the backend creates a draft with `creator: req.id`.
2. `CourseTab` loads the owned course and edits metadata, HTML description, price, and thumbnail. A FileReader previews the newly selected thumbnail.
3. A multipart PUT uploads a replacement thumbnail, optionally deletes the old Cloudinary image, and updates Course.
4. `CreateLecture` creates a Lecture and pushes its ID into Course. `LectureTab` separately uploads a video via Axios → Multer → Cloudinary.
5. Saving the lecture sends title, video metadata, and preview flag. The backend checks ownership and that the lecture belongs to the supplied course.
6. Publishing PATCH interprets `publish=true`; the catalog subsequently queries published records. The UI disables publishing when there are zero lectures, but the backend does not enforce completeness or lecture count.
7. Course deletion removes the course and its lectures/media, but does not clean purchase, enrollment, or progress references.

### D. Purchase and enrollment

1. `BuyCourseButton` posts the course ID. The backend uses the stored course price rather than a browser-supplied amount.
2. The controller creates a card-only Stripe Checkout Session in INR, multiplying rupees by 100; course/user IDs are metadata. Shipping-address collection is restricted to India.
3. A pending purchase is saved with the returned session ID, then the browser redirects to the hosted URL.
4. The signed `checkout.session.completed` webhook finds the purchase and calls `finalizePurchase`.
5. That helper marks the purchase completed and uses `$addToSet` for user/course enrollment arrays. Repeating enrollment updates avoids duplicate array entries, but these writes are not transactional or a full exactly-once guarantee.
6. Success redirects to `/course-progress/:courseId?session_id=...`. The purchase guard also retrieves/verifies the session server-side, checks paid status and user ownership, and calls the same helper.
7. The browser refetches purchase status and profile; the guard renders the player when purchased, otherwise redirects to detail. Verification errors are silently caught. Cancellation returns to detail and does not itself mark the pending record failed.

### E. Lecture progress

1. The player fetches `{data: {courseDetails, progress, completed}}`.
2. Clicking a lecture or firing the video's `onPlay` posts a view update. It does **not** wait for the video to end or measure watch percentage.
3. The backend finds/creates the user's progress record, sets/adds the lecture's viewed flag, counts viewed entries, and marks the course complete when that count equals the course lecture count.
4. The screen refetches and displays checkmarks/badges.
5. Manual completion sets only existing entries true and the course flag true. Manual reset sets those entries false. Both return 404 if there is no progress record yet.
6. No backend purchase or lecture-membership validation protects these progress operations.

### F. Profile and sales refresh

Profile saving submits name/photo as FormData → user controller → Cloudinary → User update → profile refetch → refreshed UI/auth identity. A photo is effectively required by the controller even though the UI permits name-only submission.

The instructor dashboard calls purchase listing. The backend populates completed purchases with a course creator match, filters unmatched courses, and returns instructor sales. The frontend counts records and sums amounts. Students calling this endpoint receive their own completed purchases; My Learning instead reads populated `user.enrolledCourses`.

## 8. API Integration

All paths below are relative to the configured backend origin. Prefixes are `/api/v1/...`; no production hostnames are needed. **Session** means the JWT cookie middleware. **Owner** means session plus controller-level course ownership. “Session” alone does not imply instructor or purchase authorization.

### Users — `/api/v1/user`

| Method/path | Access | Request | Successful response |
| --- | --- | --- | --- |
| POST `/register` | Public | JSON `name, email, password, role` | 200 `{success, message, user}` and cookie |
| POST `/login` | Public | JSON `email, password` | 200 `{success, message, user}` and cookie |
| GET `/logout` | Public | None | 200 `{message, success}`; clears cookie |
| GET `/profile` | Session | None | 200 `{success, user}` with enrolled courses |
| PUT `/profile/update` | Session | Multipart `name, profilePhoto` | 200 `{success, user, message}` |

### Courses/lectures — `/api/v1/course`

| Method/path | Access | Request | Successful response |
| --- | --- | --- | --- |
| POST `/` | Session; no role check | JSON `courseTitle, category` | 201 `{course, message}` |
| GET `/` | Session | None | 200 `{courses}` created by current user |
| GET `/published-courses` | Public | None | 200 `{courses}` with creator name/photo |
| GET `/search` | Session | Query `query`, comma-separated or array `categories`, `sortByPrice=low/high` | 200 `{success, courses}` |
| GET `/:courseId` | Owner | None | 200 `{course}` |
| PUT `/:courseId` | Owner | Multipart title, subtitle, description, category, level, price, optional `courseThumbnail` | 200 `{course, message}` |
| PATCH `/:courseId` | Owner | Query `publish=true` or `false` | 200 `{message}` |
| DELETE `/:courseId` | Owner | None | 200 `{message}` |
| POST `/:courseId/lecture` | Owner | JSON `lectureTitle` | 201 `{lecture, message}` |
| GET `/:courseId/lecture` | Owner | None | 200 `{lectures}` |
| POST `/:courseId/lecture/:lectureId` | Owner + membership | JSON `lectureTitle, videoInfo: {videoUrl, publicId}, isPreviewFree` | 200 `{lecture, message}` |
| GET `/lecture/:lectureId` | Owner | None | 200 `{lecture}` |
| DELETE `/lecture/:lectureId` | Owner | None | 200 `{message}` |

### Media — `/api/v1/media`

| Method/path | Access | Request | Successful response |
| --- | --- | --- | --- |
| POST `/upload-video` | Public; no auth middleware | Multipart file field `file` | 200 `{success, message, data}` containing Cloudinary result |

### Purchases — `/api/v1/purchase`

| Method/path | Access | Request | Successful response |
| --- | --- | --- | --- |
| POST `/checkout/create-checkout-session` | Session | JSON `courseId` | 200 `{success, url}` |
| POST `/checkout/session/:sessionId/verify` | Session + session/purchase ownership | No body needed | 200 `{success, message}` after paid-session verification |
| POST `/webhook` | Stripe signature, not user cookie | Raw JSON and `stripe-signature` header | Empty 200; handles checkout completion |
| GET `/course/:courseId/detail-with-status` | Session | None | 200 `{course, purchased}`; partial lecture URL sanitization |
| GET `/` | Session | None | 200 `{purchasedCourse}`; role-specific sales/purchases |

### Progress — `/api/v1/progress`

| Method/path | Access | Request | Successful response |
| --- | --- | --- | --- |
| GET `/:courseId` | Session only | None | 200 `{data: {courseDetails, progress, completed}}` |
| POST `/:courseId/lecture/:lectureId/view` | Session only | None | 200 `{message}` |
| POST `/:courseId/complete` | Session only | None | 200 `{message}` |
| POST `/:courseId/incomplete` | Session only | None | 200 `{message}` |

`GET /` on the backend returns a text health response. It is not a database readiness check.

Typical failures include 400 for required fields, invalid login or unpaid verification; 401 for absent cookie; 403 for ownership violations; 404 for missing records; 429 for rate limiting; and 500 from many controller catches. Error envelopes vary (`message`, sometimes `success`). Webhook signature failures return text with 400. Several controllers only log exceptions, so a reliable JSON error response is not guaranteed. No global custom error middleware is defined; CORS/upload parser errors use Express behavior.

## 9. Important Technical Decisions

| Observable choice | Supported effect / rationale boundary |
| --- | --- |
| Separate client and server packages | Independent frontend build and API startup. No design record explains the original hosting choice. |
| RTK Query plus an auth slice | Request state/cache lives in API reducers; identity/readiness supports navbar and guards. Why this was chosen over other libraries is undocumented. |
| Lazy page imports and Suspense | Splits route code and shows loading fallback. No measured bundle or latency improvement is supplied. |
| Raw webhook body | Explicit code comments identify signature verification as the reason. |
| Shared `finalizePurchase` helper | Reuses completion/enrollment logic in webhook and return-page verification. `$addToSet` prevents duplicate enrollment entries. |
| Owner checks in course handlers | Associates authoring operations with course creator rather than trusting the browser guard. Role enforcement remains incomplete. |
| Partial lecture URL masking | Removes non-preview URLs from detail responses for unpaid users. It does not cover the progress response. |
| Environment-based origins/cookies | Supports local and separate-origin deployment settings; actual deployment configuration is unknown. |
| Custom theme context and localStorage | Persists theme preference; no need to persist session tokens in browser-readable storage. |

## 10. Error Handling and Edge Cases

Implemented feedback includes catalog/search skeletons, guard loading text, Suspense spinners, disabled buttons during many mutations, upload progress, purchase error toasts, and explicit dashboard/detail/player load failures. Search has a no-results view; My Learning has a no-enrollment view.

Concrete gaps from the implementation:

- **Expired/invalid token:** `isAuthenticated.js` catches `jwt.verify` errors without responding or calling `next`; expired sessions may leave requests pending. JWT and cookie expiry durations also differ.
- **Missing progress:** complete/reset returns 404 until a view creates a record; the player does not display mutation error feedback for these actions.
- **Empty curriculum:** `CourseProgress.jsx` accesses `initialLecture.videoUrl` and IDs without an empty-list guard. Deleting lectures or publishing through the API can reach this state.
- **Profile photo absent:** `updateProfile` dereferences `profilePhoto.path`; name-only editing fails, and the old image may already have been scheduled for deletion.
- **Cloudinary failure:** utility catches suppress errors. A media route may report success with missing upload data; thumbnail/profile consumers can fail when accessing the result.
- **Search exceptions:** invalid raw regex input or database errors are logged without an error response. The browser search screen does not render an explicit query-error state.
- **Load failure assumptions:** `CourseTable` dereferences `data.courses`; `Profile` dereferences `user.name` without a complete error guard.
- **Form validation:** most submits are button handlers rather than native validated form submission. Input `required`/`accept` attributes are not comprehensive validation. No common validation schema, normalized emails, or password-strength checks are present.
- **Checkout timing:** verification runs in an effect while the purchase-status query also runs; timing of loading flags, redirects, and StrictMode effect repetition needs browser testing. The session query parameter is not cleared after success.
- **Search URL:** hero navigation interpolates raw text before the API later encodes it; characters such as `&` can alter the browser query. Direct `/course/search` with no query can serialize `null` as a search string.

## 11. Performance, Security, and Reliability

### Verified mechanisms

- Route-level lazy loading, RTK Query caching, skeletons, and responsive layouts are implemented.
- Bcrypt hashes passwords; login/profile responses remove password fields; JWT cookies are HttpOnly.
- Course edits/deletes and lecture operations check creator ownership. Lecture editing also checks membership.
- Stripe webhook verification uses the raw payload and configured signing secret. Explicit session verification checks paid status and user ownership. Checkout pricing comes from the database.
- CORS uses configured origins with credentials; development also accepts localhost/127.0.0.1 origins. No-origin requests are allowed.
- Backend disables `x-powered-by`, sets nosniff/frame/referrer/permissions headers, and sets HSTS for requests detected as HTTPS.
- A per-process IP counter defaults to 300 requests per 15 minutes, returns 429, and periodically cleans expired entries. Webhooks bypass this limiter.

### Concerns and limits, not claims of exploited incidents

| Priority | Finding and evidence | Recommended direction |
| --- | --- | --- |
| High | `courseProgress.controller.js` returns populated lectures for any authenticated user and never verifies purchase/enrollment. | Enforce entitlement on server reads and writes, independently of React route guards. |
| High | Purchase detail populates the complete creator document and returns it. User password is not schema-excluded. | Explicitly select public creator fields; add response tests preventing password-hash/private-field exposure. |
| High | `media.route.js` accepts unauthenticated uploads; Multer has no size/type limits. | Require upload authorization and enforce resource limits and file validation. |
| High | Description HTML is stored and rendered with `dangerouslySetInnerHTML` without visible sanitization. | Define allowed rich-text markup and sanitize; add hostile-markup tests. |
| High | Course creation checks session but not instructor role. Registration permits self-selection of instructor. | Clarify role policy and enforce it in the API, beyond UI guards. |
| Medium | Cookie authentication has no explicit CSRF token verification; SameSite=None is possible; logout uses GET. | Review CSRF defenses for state-changing requests and deployment origins. CORS alone is not authorization. |
| Medium | Checkout permits repeated purchases and lacks published/price validity checks; failed/canceled records have no transition handler. | Validate eligibility/price, prevent unwanted duplicates, reconcile payment states. |
| Medium | Purchase finalization is sequential without a transaction or unique session index. Webhook completion does not explicitly check `payment_status`, unlike the verification endpoint. | Add idempotency constraints, recovery, and explicit event/payment rules. |
| Medium | Progress accepts arbitrary lecture IDs, has no unique user/course index, and uses count equality for completion. | Validate membership, use atomic updates/uniqueness, derive completion from the actual curriculum. |
| Medium | Course deletion leaves enrollments, purchases, and progress; media failures can leave inconsistent state. | Specify deletion/retention policy and implement reliable cleanup. |
| Medium | Upload files remain on local disk; replaced lecture videos are not removed. | Add cleanup/compensation and persist reliable media IDs. |
| Medium | Search uses user-supplied regex, lists have no pagination, instructor sales loads all completed purchases before filtering populated courses. | Bound/escape searches, paginate, index query fields, and filter/aggregate in the database. |
| Medium | Rate limit Map is local to a process and resets on restart; startup does not await database readiness. | Shared rate-limit storage where needed and explicit startup/readiness handling. |

Additional observations: `trust proxy` is fixed at one hop and should match the deployment topology. No CSP configuration, monitoring integration, transaction retry layer, or dependency vulnerability audit was found/performed. A credential-like comment exists at the top of `client/src/pages/Login.jsx`; its meaning and validity were not verified, and its value is intentionally omitted here. Review it privately and rotate/remove it if it represents a real credential.

Focus/reconnect refetch flags are set on some queries, but `setupListeners` is not called in the store. Their intended browser event behavior should not be claimed as verified. API caches are not reset on logout, which creates a risk of stale user-specific data across account switches. The system theme is sampled when the theme effect runs; no operating-system theme-change subscription is installed. The Sonner wrapper uses a different theme context and is mounted outside App's custom provider.

## 12. Setup and Execution

These instructions reflect repository scripts, not a successful installation during this review. No application files were changed to repair setup.

### Prerequisites and installation

Use Node.js and npm compatible with the committed dependencies. No project engine pin or version file is present. The locked Vite package declares Node `^18.0.0 || >=20.0.0`; ESLint 9's own engine requirement is stricter than simply “Node 18.” The smoke script also requires a runtime with global `fetch`. The old README's Node 16 guidance is not sufficient evidence of compatibility.

MongoDB is required for persisted features; Cloudinary configuration is required for media; Stripe test configuration is required for checkout and webhook verification. Use disposable/test services for development.

From the repository root, the package-level installation commands are:

```sh
npm ci --prefix server
npm ci --prefix client
```

Both lockfiles exist. **The client installation is not guaranteed to work:** resolve/review the `server: file:..` local dependency mismatch noted above before relying on a clean install. That correction is a separate code/configuration task, not part of this documentation change.

Copy `.env.example` to `.env` separately inside `server/` and `client/`, then fill in private values locally. Keep secrets out of version control and out of `VITE_*` variables. Run the backend with its working directory set to `server/` because dotenv and upload paths are relative.

### Environment contract

| Variable | Scope / actual behavior |
| --- | --- |
| `VITE_BACKEND_URL` | Client backend origin without `/api/v1`; trailing slashes trimmed. Development fallback is `http://localhost:8080`; production fallback is current browser origin. |
| `MONGO_URI` | Server MongoDB connection URI. |
| `PORT` | Server listen port; fallback 3000, example uses 8080. Set 8080 to match client/smoke defaults. |
| `NODE_ENV` | Production controls default cookie security and development CORS exceptions. |
| `JWT_SECRET` | JWT signing/verifying secret; legacy fallback `SECRET_KEY`. |
| `CLOUDINARY_CLOUD_NAME`, `CLOUDINARY_API_KEY`, `CLOUDINARY_API_SECRET` | Media configuration; legacy fallbacks `CLOUD_NAME`, `API_KEY`, `API_SECRET`. |
| `STRIPE_SECRET_KEY` | Server Stripe SDK secret. |
| `WEBHOOK_ENDPOINT_SECRET` | Stripe signature verification secret; this is the name read by the controller. |
| `STRIPE_PUBLISHABLE_KEY` | Present in example, but not referenced by application code; hosted checkout uses the returned URL. |
| `FRONTEND_URL` | Comma-separated CORS origins. First entry also supplies checkout redirect base if no explicit public URL. |
| `FRONTEND_PUBLIC_URL` | Single checkout return/cancel base URL; otherwise first allowed origin, then local port 5173. |
| `COOKIE_SECURE` | If supplied, true only for string `true`; otherwise follows production mode. |
| `COOKIE_SAME_SITE` | Overrides cookie policy; default None when secure, otherwise Lax. |
| `RATE_LIMIT_WINDOW_MS`, `RATE_LIMIT_MAX` | Defaults 900000 milliseconds and 300 requests. |
| `SMOKE_BASE_URL` | Smoke process target; default `http://localhost:8080`. |

The implementation does not read the old README's `VITE_API_URL` or `STRIPE_WEBHOOK_SECRET` names.

### Commands

In one terminal:

```sh
cd server
npm run dev
```

In a second terminal:

```sh
cd client
npm run dev
```

| Directory | Command | Purpose / limitation |
| --- | --- | --- |
| `client` | `npm run build` | Vite production build into `dist` |
| `client` | `npm run preview` | Preview the built frontend; separate API still needed |
| `client` | `npm run lint` | ESLint over client files |
| `server` | `npm start` | Run `node index.js` |
| `server` | `npm run smoke` | Run five HTTP checks against an already-running API |
| `server` | `npm run build` | Existing script installs dependencies then expects a nested `server/client`; this does not match the sibling layout and should not be treated as a working monorepo build |

No `npm test` or type-check script is defined in either package.

### Deployment evidence and boundaries

`client/vercel.json` rewrites paths to `index.html` for browser navigation. `server/vercel.json` routes requests to `index.js` through `@vercel/node`. These are configuration artifacts, not evidence of a successful deployment.

The Express entry does not serve `client/dist` or define SPA fallback routing; the existing README's combined static/API hosting claim is unsupported by this source. A separate frontend host needs `VITE_BACKEND_URL` at build time, matching API CORS/cookie settings, and a valid frontend redirect base. Same-origin fallback requires actual API routing on that origin; the frontend's catch-all rewrite alone does not provide it. Local disk uploads and an in-memory limiter require particular scrutiny on serverless hosting. Live domains, service versions, database indexes provisioned outside the schemas, and provider settings were not inspected.

## 13. Testing and Quality

`server/scripts/smoke.mjs` is the only test-like executable found. It uses sequential native fetch calls and status assertions:

1. Root health returns 200.
2. Profile without auth returns 401.
3. Checkout creation without auth returns 401.
4. Progress without auth returns 401.
5. Webhook without a valid signature returns 400.

It exits nonzero if any check fails. It does not test valid login, expired tokens, role/ownership boundaries, successful purchases, paid-content protection, file uploads, progress correctness, or database consistency. It does not start the server and has no explicit request timeout, so handlers that never respond can stall it.

Frontend ESLint enables recommended JavaScript, React, hooks, and JSX-runtime rules, with React Refresh export warnings. Browser globals are configured for all client JS/JSX, including config files; no separate Node configuration is present. There are visible unused imports, missing mapped keys, and incomplete hook dependency arrays, but no lint result is claimed without running it. `@types/react` packages do not establish TypeScript checking; `jsconfig.json` primarily supplies aliases.

No unit/component/end-to-end suite, coverage setup, CI workflow, backend lint configuration, or formatter configuration was found. For this documentation-only review, dependencies were absent and no install, build, lint, smoke run, or external service mutation was performed. Validation was limited to source inspection, file-reference/section checks, and checking that existing tracked files were unchanged.

## 14. Challenges and Improvement Opportunities

Prioritize corrections using concrete user scenarios rather than broad rewrites:

1. **Paid content access:** make an authenticated but unpaid request to progress in a controlled test; it should be denied. Add entitlement and membership enforcement in `courseProgress.controller.js` and corresponding integration tests.
2. **Sensitive response fields:** constrain creator population in `coursePurchase.controller.js`; assert only public fields leave the server.
3. **Session failure behavior:** return consistent 401 responses for invalid/expired JWTs; align expiry policy; clear user-specific RTK Query caches on logout. Relevant files: `isAuthenticated.js`, `generateToken.js`, `authApi.js`.
4. **Repeatable payment completion:** test duplicate webhooks, webhook/verification races, partial database failure, missing/deleted course, and duplicate checkout requests. Add explicit state rules and indexes in the purchase model/controller.
5. **Progress semantics:** decide whether “viewed” means selected, started, or completed. Guard empty curricula, validate IDs, handle changes to course lectures, and make initial manual completion work predictably.
6. **Media lifecycle:** authorize upload, limit file size/type, clean temporary files, preserve old assets until replacement succeeds, and clean abandoned/replaced videos. Relevant files: `multer.js`, `cloudinary.js`, `media.route.js`, `LectureTab.jsx`, user/course controllers.
7. **Validation and errors:** add shared request validation and consistent error responses. Return errors from catch blocks, handle missing IDs/records, sanitize HTML, normalize email, and enforce unique constraints where needed.
8. **Clean setup:** reconcile the local package dependency, server build paths, Node version policy, and old README discrepancies. Preserve this analysis's distinction between current scripts and proposed repairs.
9. **Cache and UI consistency:** add appropriate tags, register query event listeners if desired, eliminate duplicate/manual refetch patterns where unnecessary, and test account switching and editing followed by list navigation.
10. **Smaller UI issues:** prefill profile edit name; handle failed profile/course queries; make mobile guest navigation reflect login state; synchronize toast/app themes; use `updatedAt` rather than `createdAt` for the “Last updated” label in `CourseDetail.jsx`; share duplicated category lists; add accessible labels/keyboard controls to clickable non-button elements.

The source demonstrates real integration complexity, but does not prove historical project challenges, personal ownership, deployment scale, or performance improvements. Those require separate evidence from the developer.

## 15. Interview Preparation

| Question | Answer outline grounded in this project |
| --- | --- |
| How does a request travel through the app? | React event → RTK Query mutation/query or Axios upload → Express router → cookie middleware/controller → Mongoose or provider SDK → response → cache/refetch and rendered UI. Trace a specific workflow above. |
| Why are Redux and local state both used? | Identity/readiness is global; RTK Query owns server cache; input values, selected lecture, and upload progress are local. Theme is context plus localStorage. Original library-selection rationale is not documented. |
| How does session restoration prevent redirect flicker? | App requests profile, auth lifecycle sets `authChecked`, guards wait before deciding login/role redirects. Explain what happens if middleware never responds. |
| Is a protected React route sufficient security? | No. Browser guards control navigation; API must validate identity, role, ownership, and purchase. Compare owner checks with missing progress entitlement checks. |
| How is a password handled? | Register hashes with bcrypt cost 10, login compares hashes, JWT signs userId, cookie is HttpOnly. Point out creator population as a separate response exposure gap. |
| How do cross-origin cookies work here? | Client includes credentials; server allows configured origins and credentials; Secure/SameSite settings depend on environment. Explain why production cookie settings cannot simply be copied to local HTTP. |
| What happens when a JWT expires? | Token has one-day expiry but seven-day cookie life; thrown verification errors currently log without a response. Propose a controlled 401 and frontend session handling. |
| Why preserve webhook raw bytes? | Stripe signature construction consumes the original raw Buffer; global JSON parsing is skipped on the webhook path. |
| What if the payment redirect arrives before the webhook? | Guard verifies session server-side and reuses finalization, then refetches status/profile. Discuss timing and partial-failure cases without claiming they are tested. |
| Is payment finalization idempotent? | Completed-status guard and `$addToSet` support repeats; missing unique indexes and transactions mean it is not an exactly-once multi-document operation. |
| What does progress mean? | Clicking/selecting or starting playback sets viewed, not watching to completion. Completion compares viewed count with lecture count; manual completion changes only existing entries. |
| How does RTK Query know when to refetch? | Tags invalidate some lists and screens explicitly refetch. Course removal uses the wrong list tag; focus/reconnect options lack store listener setup. |
| Why use `.unwrap()`? | It exposes fulfilled data or throws rejection for ordinary async error handling. Awaiting a mutation trigger alone yields a result object. Compare delete-course and publish handlers. |
| How are React effects used, and what can go wrong? | Effects hydrate edit state and respond to mutation flags. Missing dependencies risk stale closures; StrictMode may repeat mount effects in development, relevant to payment verification. |
| How does child-to-parent communication work? | Filter invokes a callback with selections; rich editor calls the parent's setter; course cards receive immutable record props. |
| How is media uploaded? | FormData → Axios with progress callback → Multer local file → Cloudinary → URL/public ID → separate lecture save. Upload and DB save are separate operations, creating cleanup challenges. |
| How is search implemented? | Mongo regex OR across fields plus published restriction, optional category `$in`, and price sort. Discuss regex escaping, URL encoding, pagination, and indexes. |
| How would you debug a stale course list? | Inspect mutation result, tag invalidation, query subscription/cache, navigation reuse, and manual refetch. Course removal currently invalidates lecture rather than creator-course tags. |
| What would you test first? | Unpaid progress access, sensitive creator fields, expired JWT response, duplicate payment events, owner isolation, upload rejection, empty curriculum, and name-only profile edits. |
| How does the sales chart calculate results? | Filter completed purchases to instructor courses, count sales, sum stored amounts, map one point per purchase. It is neither a date trend nor per-course aggregation. |
| Where are performance optimizations? | Lazy routes, request caching, reusable components, responsive skeleton UX. No measured speedup; list queries and instructor sales filtering remain scaling opportunities. |
| Is this React Native, and what would a port require? | It is DOM-based React with CSS, browser storage, React Router, Radix, Quill, and HTML video. A native port needs navigation, native controls/media/file handling, and secure session storage decisions. None is implemented here. |
| What JavaScript concepts are visible? | Async/await, promises, object spread for form state, optional chaining, array map/filter/reduce/some, ES modules, closures, event callbacks, and URL encoding. Discuss examples in Dashboard, Filter, and API builders. |
| What happens when a course is deleted after purchase? | Current delete flow removes course/lectures but leaves other records. Populated course references may become null; define retention and access policy before implementing cleanup. |

A concise project explanation: “This is a React and Express learning application with MongoDB course/enrollment data, Cloudinary media, and Stripe Checkout. RTK Query connects student and instructor screens to the API. The source includes course authoring, hosted payment verification, and lecture progress; server authorization, validation, and reliability have concrete gaps I can explain and prioritize.”

## 16. Resume-Relevant Contributions

These are **candidate descriptions of repository-supported work**, not evidence that a particular person authored it. Use only statements matching your actual contribution; do not claim invented adoption, revenue, latency gains, team leadership, or production readiness.

| Candidate contribution | Supporting source |
| --- | --- |
| Built React student and instructor workflows for course discovery, authoring, enrollment, and video learning. | `client/src/App.jsx`, `pages/student/`, `pages/admin/` |
| Integrated Redux Toolkit and RTK Query for authentication state, API requests, caching, and mutation feedback. | `app/`, `features/authSlice.js`, `features/api/` |
| Implemented Express/Mongoose course and lecture APIs with creator-ownership checks. | `server/routes/course.route.js`, `controllers/course.controller.js`, `models/` |
| Integrated Stripe Checkout with signed webhook processing and authenticated checkout-session verification. | `server/controllers/coursePurchase.controller.js`, purchase route, purchase guard |
| Implemented Cloudinary media uploads and lecture video metadata management with frontend upload feedback. | `LectureTab.jsx`, `server/routes/media.route.js`, `utils/cloudinary.js` |
| Built persisted lecture-view tracking and manual course completion/reset controls. | `CourseProgress.jsx`, progress API/controller/model |
| Created responsive reusable UI, theme switching, rich-text editing, and instructor sales visualization. | `components/ui/`, `ThemeProvider.jsx`, `RichTextEditor.jsx`, `Dashboard.jsx` |
| Added route-level lazy loading and backend HTTP smoke checks. | `App.jsx`, `server/scripts/smoke.mjs` |

Do not describe the project as React Native, complete server-side role/paid-content enforcement, real-time websocket tracking, verified production-ready infrastructure, measured performance improvement, or comprehensive automated testing. Each would exceed the inspected evidence.

## 17. Knowledge Gaps to Address

Study in this order to explain and maintain the code confidently:

1. **Authentication versus authorization:** cookie/JWT lifecycle, server role/owner/entitlement checks, CSRF, public response projection, and rich HTML sanitization. Apply directly to the progress and creator-response findings.
2. **Payment lifecycle and consistency:** hosted sessions, raw-body signatures, webhook retries, idempotency, unique indexes, multi-document transactions, and reconciliation after partial failure.
3. **React and RTK Query:** effects/dependencies/StrictMode, query cache identity, invalidation tags, `.unwrap()`, account-switch cache cleanup, and listener registration.
4. **Express asynchronous errors and validation:** always completing requests, middleware order, typed/validated boundaries, uniform error envelopes, and database readiness.
5. **MongoDB/Mongoose modeling:** references/population, embedding progress, unique compound indexes, atomic updates, deletion policy, pagination, and search indexes.
6. **Media security and lifecycle:** multipart limits, MIME inspection, temporary storage, remote asset IDs, replacement cleanup, and delivery authorization.
7. **Testing:** separate unit, component, API integration, and end-to-end tests; test authorization failures and payment retries before expanding cosmetic coverage.
8. **Build/deployment:** package relative paths, lockfiles, Node compatibility, build-time Vite variables, SPA rewrites, cookie/CORS topology, and serverless filesystem limitations.
9. **UX/accessibility:** controlled forms, proper labels/keys, keyboard navigation, empty/error states, consistent themes, and honest progress semantics.
10. **React Native only if a future port is planned:** learn native navigation, storage, uploads, and video lifecycle as a separate project scope; do not present those as experience demonstrated here.

### Review scope and exclusions

Inspected all first-party application modules across `client/src` and the backend routes/controllers/models/middleware/utilities, the smoke script, package manifests, lockfile metadata, environment examples, build/style/lint/deployment configuration, and existing documentation. Static SVG assets were inventoried; their artwork was not analyzed. Lockfiles were inspected for dependency/layout/runtime evidence rather than a transitive-package security audit. Git internals/history and external provider accounts were outside scope. No relevant first-party repository area was inaccessible. Dependencies, private environment configuration, running services, production data, and deployed behavior were unavailable or not exercised. Only this new analysis document was created; existing documentation and application files were preserved.
