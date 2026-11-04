# Resume optimization rulebook

Created 22 October 2026 for an early-career engineer with 6+ months of Sofmen mobile frontend experience. This library guides future resume tailoring; it does not replace the candidate's resume or claim new skills.

## Start here

1. Read the target job description and identify its actual responsibilities, minimum experience, education and stack gates.
2. Load [SHARED_RESUME_RULES.md](SHARED_RESUME_RULES.md) and the matching role file below.
3. Read [CANDIDATE_EVIDENCE.md](CANDIDATE_EVIDENCE.md), then the latest resume, READMEs and relevant available source code. Keep reported work, documented capabilities and personal authorship distinct.
4. Tailor only supported summary, skills, experience and project evidence. Preserve genuine metrics and user-facing outcomes.
5. Write bullets with **content-driven lengths and no fixed character or word limit**. Keep related details together, avoid repetitive phrasing, and check unsupported claims, duplication and rendered layout.
6. Preserve the current template, spacing, fonts, margins and section order. Only the user changes formatting manually. Fit content onto exactly one page through editing and prioritization, then compile and check the result.
7. Deliver revised content with a concise review of relevance, evidence and repetition, confirmation of unchanged formatting and verified one-page output, plus remaining evidence gaps.

Truthfulness, readable detail and natural phrasing apply to all versions. Bullet lengths may vary; do not pad, truncate or force identical sentence patterns for visual uniformity. The target JD determines relevance; role guides determine emphasis; source frequency is a secondary aid. No keyword or template authorizes an unsupported claim. Examples are alternatives across versions, not fixed-length templates or a bank to paste wholesale into one resume.

## Choose a version

| Main hiring emphasis | Instructions | Candidate emphasis |
| --- | --- | --- |
| General implementation judgment, problem solving and CS concepts | [SDE](SDE/RESUME_INSTRUCTIONS.md) | Explainable implementation decisions, honest coursework and verified achievements |
| Maintainable features, defects and team delivery | [Software Developer / Engineer](Software_Developer/RESUME_INSTRUCTIONS.md) | Real Sofmen workflow plus a distinct project contribution |
| UI, server and persistence in one feature path | [Full Stack](Full_Stack_Developer/RESUME_INSTRUCTIONS.md) | Attributable TaleCrafter workflow; professional frontend context remains explicit |
| Server logic, API implementation and data | [Backend](Backend_Developer/RESUME_INSTRUCTIONS.md) | Personal server work, not mobile API consumption relabeled as backend |
| Browser interfaces and web interaction | [Frontend](Frontend_Developer/RESUME_INSTRUCTIONS.md) | TaleCrafter web evidence plus accurately labeled mobile delivery |
| Device applications, navigation and platform behavior | [Mobile](Mobile_Application_Developer/RESUME_INSTRUCTIONS.md) | React Native professional exposure and attributable mobile contributions |

SDE and Software Engineer titles overlap. Choose by duties rather than title prestige. For hybrid roles load one primary guide plus the relevant sections of a secondary guide, then deliver one coherent resume. Native Android/iOS and Flutter listings require evidence beyond React Native; a Java full-stack posting does not become a match by inserting Spring Boot.

## Research and evidence

[RESEARCH_METHODOLOGY.md](RESEARCH_METHODOLOGY.md) contains the source register, exclusions, sampling rules and limitations. [KEYWORD_MASTER_REFERENCE.md](KEYWORD_MASTER_REFERENCE.md) maps 46 coded concepts across 15 categories with role-specific n/N counts, source links and inclusion conditions. [research/postings.json](research/postings.json) retains separate required, preferred and responsibility annotations.

**35 unique descriptions, 43 role assignments:** SDE 6, Software Developer 6, Full Stack 9, Backend 7, Frontend 6, Mobile 9. Some descriptions belong to two relevant cohorts. The requested 20–30 per role was not achieved; the smaller convenience sample supports cautious tailoring guidance, not market-wide rates. Broader experience ranges, internship/junior mixes and unknown numerical eligibility are flagged. Accessible pages are not guaranteed active vacancies.

Main implications: titles overlap; full-stack hiring spans multiple ecosystems; browser and native UI evidence differ; API integration must stay separate from server development. The existing professional experience is most directly aligned with React Native/mobile work. Backend/full-stack positioning depends more heavily on attributable personal-project work. That fit assessment is an editorial inference from the supplied evidence, not a hiring prediction.

## Review and upkeep

Save successfully compiled and checked final PDFs in [final-resumes/](../final-resumes/), with separate folders for `SDE`, `Software_Developer`, `Full_Stack_Developer`, `Backend_Developer`, `Frontend_Developer` and `Mobile_Application_Developer`. Use `Aditya_Shriwas_<Role>_YYYY-MM-DD.pdf`; add a company/job identifier for targeted variants and a version suffix for filename collisions. Retain previous finals. Temporary output remains in `build/`, and the original reference PDF stays at the workspace root.

Review each bullet for one coherent contribution, enough relevant detail, truthful outcomes and distinct wording. Compile and confirm exactly one page with unchanged formatting, readable wrapping and no overflow or clipping, then check extracted text for reading order. If rendering is unavailable, report page fit as unverified. Character-count scripts are no longer needed.

If research changes, update the posting annotations, source register, keyword counts and affected role guidance together. Recheck source freshness; do not change the collection date without new research. The original collection checked all 276 frequency cells. Review examples for evidence and clarity after edits.

## Future request example

“Tailor my resume for this backend job using resume-rulebook/Backend_Developer/RESUME_INSTRUCTIONS.md and SHARED_RESUME_RULES.md. Review current candidate evidence, preserve true impact, use natural bullet lengths with related details grouped coherently, preserve the current layout and spacing, fit the content onto exactly one page, and report repetition, unsupported claims and evidence gaps.”

Research limits are in the methodology; candidate verification gaps are in the evidence ledger. The original resume and project evidence are retained. One-time setup scripts, redundant QA reports and generated build artifacts have been removed.
