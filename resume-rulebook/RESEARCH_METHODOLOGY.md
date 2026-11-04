# Research methodology and source register

Collection date: **22 October 2026 (Asia/Kolkata)**. This is an exploratory, purposive sample of publicly accessible Indian engineering descriptions, not a representative labor-market survey or a list of guaranteed active vacancies.

## Collection and eligibility

Searches combined India and city names with each role family, junior/fresher/graduate, 0–1, 0–2, 1–2 and related experience phrases. Additional searches targeted employer career sites, Lever, Workday, Cisco and Amazon to diversify beyond small service companies. Sources below include employer pages, employer ATS pages and one Wellfound employer listing. Job-board search results were discovery aids, not counts of vacancies.

Retain Indian roles with explicit early-career eligibility or an explicitly marked adjacent range. Retain junior roles without numeric years as exploratory evidence, not proof of 0–2 eligibility. Read bodies rather than trusting title or search filters. Do not turn a 1+ minimum into a six-month match. No applications, messages or accounts were submitted.

**The 20–30 postings per role target was not reached.** Search results repeatedly included duplicates, aggregate pages, senior roles, older PDFs and closed jobs. This pass prioritizes a smaller traceable set rather than padding the denominator. Frontend/mobile evidence is particularly service-company heavy. The sample includes enterprise Jio/American Express, large technology Amazon, product employers Hevo/Shipthis/New Street/Teal, and consulting/services employers such as Allata; this diversity does not establish market representativeness. Employer descriptions and recruitment authenticity were not independently audited.

## Unit, overlap and retrieval

One company + distinct role/requisition is one record. A role can appear in two cohorts only where its title/body explicitly spans those responsibilities: e.g. Allata's Software Engineer is full-stack, and Hevo's SDE I is in Backend Engineering. Each such posting is counted once within each applicable role, once globally. SDE and generic Software Engineer cohorts are not artificially pooled to reach a target.

J04 (0–3), J32 and J34 (1–3), J33 (1+ without upper bound), J11 (1+ with projects allowed), and J35 (junior without numeric range) are flagged broader/uncertain eligibility. Counts below include them, so they are early-career-oriented, not strict 0–2-only denominators. J25/J26 are internship/junior openings; J23 has mixed internship/full-time labels.

J18 and J19 had usable search-rendered bodies but empty direct-open responses. J31 had a usable search-rendered body and a failed direct open. These remain explicitly lower-confidence retrievals; no inaccessible fields were invented. Publication dates are null where unavailable. Relative crawl/posting ages are not converted to invented exact dates. An accessible description or Apply button does not independently confirm that recruitment remains active.

## Coding and frequency

The machine-readable register is [postings.json](research/postings.json). Each record has source, title, location, experience, access date, publication date when known, notes and three separate annotation arrays: **required**, **preferred**, **responsibilities**. These are conservative concept annotations from the retrieved JD, not a full copied JD. Keywords in a working stack or duties are not automatically mandatory. A term can occur in more than one status where the source itself uses both; J35's required TypeScript/Node and preferred other languages share the broad programming concept.

For each concept and role: numerator = distinct relevant records with that concept in any of the three arrays; denominator = all retained records assigned to that role. A zero means not coded in available text, not proof employers never expect it. Equivalent wording is grouped at concept level; alternatives such as React/Angular count as a React mention, not an exclusive React requirement. Broad API mentions do not count as Express.js, and database mentions do not imply SQL. No percentages or industry-wide probabilities are inferred.

The master table reports every observed n/N, evidence IDs, and editorial priority. **Core** means at least half the cohort and at least three mentions; **Important** means at least two mentions below that threshold; **Optional** means one mention. These small-sample labels are an organizing aid. **Conditional** is a separate truth/target-JD gate applying even to Core; **Avoid** applies to unsupported, irrelevant or misleading claims. Role files explain practical priorities and label extrapolated resume advice as general professional guidance. A target JD always overrides prevalence for its own requirements.

## Sample sizes

| Role | Descriptions | Source IDs |
| --- | ---: | --- |
| SDE | 6 | J01, J02, J03, J04, J32, J33 |
| Software Developer | 6 | J05, J06, J07, J08, J09, J31 |
| Full Stack Developer | 9 | J05, J09, J10, J11, J12, J13, J14, J31, J35 |
| Backend Developer | 7 | J03, J08, J15, J16, J17, J18, J32 |
| Frontend Developer | 6 | J19, J20, J21, J22, J23, J34 |
| Mobile Application Developer | 9 | J13, J14, J24, J25, J26, J27, J28, J29, J30 |

**35 unique descriptions; 43 role assignments.** Overlap is not independent evidence.

## Source register

All accessed 2026-10-22. Each linked ID resolves to its JD. Required, preferred and responsibility concept annotations are inspectable in the JSON register; these notes preserve stack and eligibility distinctions.

### J01 — Amazon

[Software Dev Engineer I — University Talent Acquisition](https://www.amazon.jobs/en-gb/jobs/10454438/software-dev-engineer-i-amazon-university-talent-acquisition) — Bengaluru / Hyderabad / Chennai. Experience: 2026 graduates; no years minimum.

Roles: SDE. Publication: not available. Description retrieved. Accessible listing; vacancy not independently confirmed.

2026 degree cohort is mandatory; algorithms and distributed systems are preferred here, not universal entry requirements.

### J02 — Heizen

[Software Development Engineer](https://www.heizen.work/careers/software-engineer) — Remote / Hyderabad. Experience: 0+ on detail; 0–2 in index.

Roles: SDE. Publication: not available. Description retrieved. Accessible listing; vacancy not independently confirmed.

Database, API and cloud knowledge may be learned; ownership language describes prospective responsibilities.

### J03 — Hevo Data

[SDE I](https://jobs.lever.co/hevodata/6cbbe304-e065-4711-bf3e-756795d2bc2a) — Bangalore. Experience: 0–1 years.

Roles: SDE, Backend_Developer. Publication: not available. Description retrieved. Accessible listing; vacancy not independently confirmed.

Java/TypeScript/Python alternatives; concurrency is a learning area, not an expert prerequisite.

### J04 — Jio

[Software Development Engineer I — 86612448](https://careers.jio.com/frmjobdescription.aspx?JBTITLE=gWTG74MfkJuQf63qpCNAqw%3D%3D&funcCode=am1AraTXtX4%2FW%2FEJK6dzzw%3D%3D&jbID=BJK5KQQgKnRtYB1AKswtAQ%3D%3D) — Mumbai. Experience: 0–3 years; broader range.

Roles: SDE. Publication: 2026-07-29. Description retrieved. Accessible listing; vacancy not independently confirmed.

Posted 2026-07-29. Skills list also includes SDLC. Wider experience band retained explicitly.

### J05 — Born West

[Entry Level Software Developer](https://www.bornwest.com/careers/entry-level-software-developer) — Remote India. Experience: 0–2 years.

Roles: Software_Developer, Full_Stack_Developer. Publication: not available. Description retrieved. Accessible listing; vacancy not independently confirmed.

Rails is the working stack, prior Rails exposure is preferred; does not justify adding Ruby to this candidate.

### J06 — New Street Technologies

[Associate Software Engineer](https://newstreettech.com/careers/associate-software-engineer) — Bengaluru. Experience: 0–1 year.

Roles: Software_Developer. Publication: not available. Description retrieved. Accessible listing; vacancy not independently confirmed.

Working Python required; mobile, FastAPI and AI coursework are optional exposure.

### J07 — Heizen

[Associate Software Engineer](https://www.heizen.work/careers/associate-software-engineer) — Remote / Hyderabad. Experience: 0–2 years.

Roles: Software_Developer. Publication: not available. Description retrieved. Accessible listing; vacancy not independently confirmed.

One language suffices: Python, Java or JavaScript/TypeScript among examples.

### J08 — Advance Sensing

[Software Engineer](https://advancesensing.com/pages/careers/careers/software-engineer.html) — Bengaluru. Experience: 0–2 years.

Roles: Software_Developer, Backend_Developer. Publication: not available. Description retrieved. Accessible listing; vacancy not independently confirmed.

Python and basic Java; industrial integrations. TCP/IP and multithreading are preferred, not universal.

### J09 — Allata / IMRIEL

[Junior Software Engineer](https://jobs.lever.co/Allata/a0eb9210-f90b-4a71-9107-765fb4848001) — Vadodara. Experience: 6 months–1 year.

Roles: Software_Developer, Full_Stack_Developer. Publication: not available. Description retrieved. Accessible listing; vacancy not independently confirmed.

C#/.NET and Angular or React; SQL joins/indexes. React tag denotes an accepted alternative, not an exclusive requirement.

### J10 — Shipthis

[Full Stack Developer](https://shipthis.freshteam.com/jobs/4cXVjGaL2Ywj) — Bengaluru. Experience: 0–2 years.

Roles: Full_Stack_Developer. Publication: not available. Description retrieved. Accessible listing; vacancy not independently confirmed.

Python/Node.js, Angular/Ionic and MongoDB; module lifecycle and deployment responsibilities.

### J11 — SCOI

[Junior Full Stack Developer](https://www.scoi.io/careers/junior-full-stack-developer) — Pune hybrid. Experience: 1+; internships and strong projects count.

Roles: Full_Stack_Developer. Publication: not available. Description retrieved. Accessible listing; vacancy not independently confirmed.

Node.js required; NestJS/Next.js exposure preferred; basic deployment awareness rather than AWS expertise.

### J12 — Teal India

[Software Engineer — Full Stack](https://wellfound.com/jobs/4246030-software-engineer-full-stack-yoe-0-2?autoOpenApplication=true) — Bangalore Urban. Experience: 0–2 years.

Roles: Full_Stack_Developer. Publication: not available. Description retrieved. Accessible listing; vacancy not independently confirmed.

LLM API experience explicitly sought. Node/Express/Next/Mongo are working stack; JS/TS and React called bonus. Employer listing on Wellfound.

### J13 — OMS Software

[Junior Full Stack Developer — React Native + Spring Boot](https://omssoftware.com.au/careers/junior-full-stack-developer-react-native-and-spring-boot/) — Bengaluru. Experience: 1–2 years.

Roles: Full_Stack_Developer, Mobile_Application_Developer. Publication: not available. Description retrieved. Accessible listing; vacancy not independently confirmed.

WatermelonDB required; Java/Spring Boot basics. Play publishing familiarity or willingness to learn; iOS submission preferred.

### J14 — Heizen

[Software Engineer — Fullstack + React Native](https://www.heizen.work/careers/software-engineer-fullstack-app) — Remote / Hyderabad. Experience: 0–2 years.

Roles: Full_Stack_Developer, Mobile_Application_Developer. Publication: not available. Description retrieved. Accessible listing; vacancy not independently confirmed.

Full-stack/mobile focus; SQL/NoSQL, REST and cloud may be learned. Does not imply every React Native role includes backend ownership.

### J15 — Shipthis

[Backend Developer](https://shipthis.freshteam.com/jobs/P3OR_G1LPfuk) — Bengaluru. Experience: 0–2 header; minimum 1 year Python/MongoDB.

Roles: Backend_Developer. Publication: not available. Description retrieved. Accessible listing; vacancy not independently confirmed.

Python and MongoDB required; FastAPI and Angular appreciated. The minimum in the body overrides the zero-year header.

### J16 — Pace

[Backend Developer](https://www.pacefin.in/careers/sof) — Gurugram. Experience: 0–2 years or degree preferred (ambiguous wording).

Roles: Backend_Developer. Publication: not available. Description retrieved. Accessible listing; vacancy not independently confirmed.

Python/Django/PostgreSQL and pandas/NumPy; Redis/WebSockets/Bash/trading are preferred. Confirm ambiguous eligibility.

### J17 — Wolvion

[Backend Developer — Node/Python](https://www.wolvion.com/careers) — Remote India. Experience: 0–2 years.

Roles: Backend_Developer. Publication: not available. Description retrieved. Accessible listing; vacancy not independently confirmed.

Short role description: REST/integrations, business logic and monitoring; no separate mandatory skills list.

### J18 — SquareUp

[Junior Backend Developer](https://www.squareupdigital.com/careers) — New Delhi; onsite. Experience: 0–2 years.

Roles: Backend_Developer. Publication: not available. Search-rendered description; direct retrieval empty or failed. Accessible listing; vacancy not independently confirmed.

Spring Boot/Express alternatives. Search-rendered role body available; direct open returned empty. Lower retrieval confidence.

### J19 — Genyx Labs

[Junior Frontend Engineer](https://www.genyxlabs.com/careers/junior-frontend-engineer) — Greater Noida. Experience: 0–2 years professional or serious projects.

Roles: Frontend_Developer. Publication: not available. Search-rendered description; direct retrieval empty or failed. Accessible listing; vacancy not independently confirmed.

Framework required, React preferred. WCAG AA is work scope, not proof of candidate compliance. Search body available; open empty.

### J20 — Narix Labs

[Frontend Engineer — Junior](https://www.narixlabs.com/careers/frontend-engineer-junior) — Jaipur; remote/on-site conflict. Experience: 6 months–2 years; internships/projects accepted.

Roles: Frontend_Developer. Publication: not available. Description retrieved. Accessible listing; vacancy not independently confirmed.

Figma/Tailwind preferred; scoped work and written updates. Resolve contradictory remote versus on-site statements before applying.

### J21 — Overt Minds

[Frontend Developer — Angular / React](https://overtminds.com/blog-new/careers/frontend-developer-angular-react/) — Bangalore North. Experience: 0–1.5 body; 0–1 metadata.

Roles: Frontend_Developer. Publication: 2026-01-15. Description retrieved. Accessible listing; vacancy not independently confirmed.

Posted 2026-01-15; Angular or React, Redux or Zustand, build tools. Basics appear under responsibilities; not relabeled mandatory.

### J22 — RED SAG

[Junior Frontend Developer](https://www.red-sag.com/careers/apply/junior-frontend-developer) — Tiruppur. Experience: 0–1 year.

Roles: Frontend_Developer. Publication: not available. Description retrieved. Accessible listing; vacancy not independently confirmed.

Application deadline 2026-12-31; projects and attention to detail matter.

### J23 — Wolvion

[Frontend Developer — HTML/CSS/JS](https://www.wolvion.com/careers) — Remote India. Experience: Internship / fresher; full-time label.

Roles: Frontend_Developer. Publication: not available. Description retrieved. Accessible listing; vacancy not independently confirmed.

Semantic HTML and vanilla JS; framework-free counterexample. Employment classification needs confirmation.

### J24 — Avenio Solutions

[App Developer](https://aveniosolutions.com/careers) — Bengaluru. Experience: 0–2 years.

Roles: Mobile_Application_Developer. Publication: not available. Description retrieved. Accessible listing; vacancy not independently confirmed.

Flutter, React Native or native iOS/Android are alternatives; asynchronous programming required.

### J25 — Xenotix Labs

[React Native Junior Developer](https://www.xenotixlabs.com/careers/) — Remote / hybrid India; office context Delhi NCR / Bengaluru. Experience: Students / freshers; internship or junior.

Roles: Mobile_Application_Developer. Publication: not available. Description retrieved. Accessible listing; vacancy not independently confirmed.

Components/props/hooks; AsyncStorage, React Navigation and Firebase optional. Separate junior opening from site's 2–5 year full-stack role.

### J26 — Xenotix Labs

[Flutter Junior Developer](https://www.xenotixlabs.com/careers/) — Remote / hybrid India; office context Delhi NCR / Bengaluru. Experience: Students / freshers; internship or junior.

Roles: Mobile_Application_Developer. Publication: not available. Description retrieved. Accessible listing; vacancy not independently confirmed.

Dart/widgets/layouts; Provider/Riverpod/BLoC optional. Same employer/page as J25; distinct role.

### J27 — Cyber Infrastructure (CIS)

[React Native Developer Freshers](https://career.cisin.com/job/react-native-developers-jobs-for-freshers?PageSpeed=noscript) — Indore. Experience: 0–1 year.

Roles: Mobile_Application_Developer. Publication: 2026-08-03 (republished). Description retrieved. Accessible listing; vacancy not independently confirmed.

Republished 2026-08-03; open through 2027-08-04. SQL Server/MySQL familiarity; framework examples include Node, imprecisely.

### J28 — New Street Technologies

[Software Engineer — Android](https://newstreettech.com/careers/software-engineer-android) — Bengaluru. Experience: 1–2 professional Android years.

Roles: Mobile_Application_Developer. Publication: not available. Description retrieved. Accessible listing; vacancy not independently confirmed.

Android SDK/lifecycle; Compose, coroutines and release exposure preferred despite Compose appearing in work scope.

### J29 — Daydreamsoft

[IOS Developer](https://daydreamsoft.com/career/ios-developer-jobs-in-surat) — Surat. Experience: 0–2 years.

Roles: Mobile_Application_Developer. Publication: not available. Description retrieved. Accessible listing; vacancy not independently confirmed.

Swift or Objective-C/Cocoa Touch; Core Data/Animation, threading and memory tooling. Native track, not transferable skill labels for RN.

### J30 — Zeqo

[UI/UX Designer & Flutter Developer](https://zeqo.in/careers) — Remote / Mumbai. Experience: 0–2 years.

Roles: Mobile_Application_Developer. Publication: not available. Description retrieved. Accessible listing; vacancy not independently confirmed.

Hybrid design/development role asks for Figma-to-Flutter, visual craft and a demonstrable app.

### J31 — American Express

[Software Engineer I — 26014930](https://careers.americanexpress.com/en/sites/CX_1/job/26014930/?intlink=us-amex-career-en-us-navigation-jobs) — Gurugram hybrid. Experience: 1–2 professional years.

Roles: Software_Developer, Full_Stack_Developer. Publication: 2026-10-16. Search-rendered description; direct retrieval empty or failed. Accessible listing; vacancy not independently confirmed.

Posted 2026-10-16; closes 2026-10-23. Search body retrieved; direct open failed. Python/React with virtualization work; not a six-month match.

### J32 — Safe Security

[Software Development Engineer I](https://jobs.lever.co/safe/91e23536-2a94-496c-9c74-9dbfd815923f) — Bengaluru. Experience: 1–3 professional years; broader range.

Roles: SDE, Backend_Developer. Publication: not available. Description retrieved. Accessible listing; vacancy not independently confirmed.

Go/Python/Node alternatives; distributed systems, Kubernetes and LLM fundamentals. Stretch comparator; not beginner baseline.

### J33 — Amazon

[Software Development Engineer I — IESP Merchant Tech](https://www.amazon.jobs/en/jobs/10511883/software-development-engineer-i-iesp-merchant-tech) — Bengaluru. Experience: 1+ non-internship professional years.

Roles: SDE. Publication: not available. Description retrieved. Accessible listing; vacancy not independently confirmed.

Do not count projects or internships toward the explicit professional minimum.

### J34 — Unico Connect

[Frontend Developer — React](https://unicoconnect.com/careers/frontend-react-developer) — Mumbai. Experience: 1–3 production React/TypeScript years; broader range.

Roles: Frontend_Developer. Publication: not available. Description retrieved. Accessible listing; vacancy not independently confirmed.

Core Web Vitals and test tools in work scope; Storybook, server components and motion preferred. Stretch comparator.

### J35 — Aganitha

[Jr Full Stack Developer](https://www.aganitha.ai/careers/jr-full-stack-developer/) — Hyderabad hybrid. Experience: Junior; numerical experience unspecified.

Roles: Full_Stack_Developer. Publication: not available. Description retrieved. Accessible listing; vacancy not independently confirmed.

Junior title but extensive DevOps expectations. Python/Java/C++ are preferred; TS/Node is required. Numeric eligibility unknown.

## Exclusions and contextual checks

These are not included in frequencies:

| Source | Reason |
| --- | --- |
| [SuperAGI SDE I](https://www.foundit.in/job/software-development-engineer-i-sde-i-superagi-bengaluru-bangalore-48074691) | Explicitly closed on opening; search snippet had appeared viable. |
| [Concentrix Java Full Stack](https://jobs.concentrix.com/th/job/?id=R1768581) | Direct page says job not found despite search result. |
| [Ford Associate Software Engineer](https://apply.ford.com/en/sites/CX_1/job/71879) | Deadline 2026-10-11, before collection. Useful Java/backend contrast only. |
| [Flex Associate Software Engineer](https://flextronics.wd1.myworkdayjobs.com/en-US/Careers/job/Associate-Software-Engineer_WD230583) | Deadline 2026-10-07. |
| [Boeing Associate Software Engineer](https://boeing.wd1.myworkdayjobs.com/en-US/EXTERNAL_CAREERS/job/Associate-Software-Engineer_JR2026517817-1) | Deadline 2026-09-27. |
| [Gartner Associate Software Engineer](https://gartner.wd5.myworkdayjobs.com/en-US/EXT/job/Associate-Software-Engineer_111854-1) | Deadline 2026-09-18. |
| [Citi Junior Full Stack](https://jobs.citi.com/job/chennai/full-stack-java-developer-junior/287/102161055520) | Body requires 2–4 years despite junior title. |
| [OMS backend](https://omssoftware.com.au/careers/java-backend-developer/) and [frontend](https://omssoftware.com.au/careers/frontend-developer-angular/) | 3–5 and 2–4 years respectively. |
| [Amgen Software Development Engineer I](https://careers.amgen.com/en/job/hyderabad/software-development-engineer-i/87/95987917728) | Body describes SDET/test engineering, not target development cohort. |
| [Cisco apprentice](https://cisco.wd5.myworkdayjobs.com/Cisco_Careers/job/Bangalore-India/Software-Engineer-Trainee---Technical-Graduate-Apprentice---India-UHR_2013753) | Explicit potential-opportunity notice; not confirmed active vacancy. |
| [Viral Technologies](https://www.viraltech.in/careers.php) | Undated legacy J2ME/BlackBerry context; unsuitable basis for current stack priorities. |
| [iGreenTech iOS](https://www.igreentechservices.com/career/apply/ios-developers) | Search description exists but direct fetch cache miss; not needed to add another lower-confidence source. |
| [Hirebase mobile index](https://www.hirebase.in/jobs/mobile-development) | Aggregation mixes senior roles and 0–2 labels; no index counts used as posting evidence. |

LinkedIn search exposed some readable descriptions; these were not necessary for the final coded sample. Naukri/Indeed coverage was not established and no login or paywall bypass was attempted. No claim is made that those boards were exhaustively searched or technically blocked.

## Findings and uncertainty

Implementation, integration, code quality and team communication recur, but the responsibilities overlap substantially. SDE does not universally mean DSA-only hiring: J01 places algorithms in preferences; J03 explicitly asks for DSA and OOP; J04 emphasizes lifecycle, SQL and debugging. The SDE emphasis on algorithmic judgment is therefore a useful tailoring strategy when the JD supports it, not a universal title distinction.

Full-stack includes Rails/React (J05), .NET/Angular-or-React (J09), Python/Angular (J10) and mobile/Spring Boot (J13), so MERN is not a universal requirement. Backend examples include Python/Django and multiple language alternatives; API integration alone is insufficient backend evidence. Frontend includes vanilla JavaScript (J23), while native Android/iOS and Flutter postings prevent equating all mobile hiring with React Native.

AI coding assistants appear in some descriptions and LLM integrations in others; those are distinct capabilities. Neither warrants inventing ML expertise. Education, graduation year and minimum professional tenure must be checked independently of keyword match.

Refresh the specific target JD on every resume update. For a future broader research pass, expand each cohort toward 20–30, add more enterprise/frontend/mobile employers and non-JavaScript stacks, retain exclusions, review overlapping requisitions, and recalculate rather than merging percentages from incompatible samples.
