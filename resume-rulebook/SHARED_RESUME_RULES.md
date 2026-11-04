# Shared resume rules

These are general professional writing rules and explicit user constraints, not statistical findings about ATS systems. Apply them with the matching role file, the target job description and [candidate evidence](CANDIDATE_EVIDENCE.md).

## Truth and attribution

Use the actual title **Junior Software Engineer**, Sofmen Technologies, and verified dates. Describe real team delivery without senior, lead, architect or sole-owner implications. Per user preference, do not include an experience month count in the summary. Separate professional work, personal work and studied project capabilities. A target employer's desired responsibility is not evidence that the candidate has performed it.

For each proposed claim record: source, professional/project/coursework context, personal contribution, implementation status, outcome and confidence. Prefer user-confirmed work and attributable changes; source presence establishes implementation, not authorship. See the ledger for known gaps. Do not list a technology professionally just because a personal project uses it.

## Role-specific summary

Write each summary around the target role and the actual JD when provided. Emphasize supported responsibilities and core competencies, not a list of languages or frameworks. For language-agnostic roles such as general SDE, keep technology names in skills and project evidence. Do not add unsupported JD keywords or an experience month count.

## Fixed layout and mandatory one-page output

**Edit content only. Preserve the current resume’s layout and spacing; the user controls formatting changes manually.** Take the current `main.tex` as the formatting baseline at the start of each update, including any manual changes the user has made since the previous version.

Keep page size, margins, font family and sizes, line and paragraph spacing, section and bullet spacing, indentation, alignment, columns, header structure, headings, section order and LaTeX formatting definitions unchanged. Do not adjust geometry, spacing commands, list settings, font settings or layout environments to fit new text. Role-specific section suggestions must operate within the existing template and cannot authorize moving or redesigning sections. Relevant skills and project entries may be prioritized within their existing sections using the same formatting.

Preserve the user-requested conventions: hyphen (-) markers for project points and contact details on the first header line and the portfolio, LinkedIn and personal GitHub links together on the second line, displaying their full profile paths. Keep the personal GitHub profile link in the header; omit only project repository GitHub links and preserve Live links. ExpressU leads the current SDE project list.

**The final resume must occupy exactly one page.** Fit the content by removing redundant wording, combining related points and selecting the most relevant supported information. Bullets still have natural lengths with no fixed character or word limit. Do not shrink text, reduce spacing, widen margins, introduce negative spacing, clip content or hide overflow. Preserve genuine metrics, truthful scope and meaningful user outcomes in retained claims; do not distort a claim just to save space.

After each actual resume update, compile with the existing build setup. Verify the PDF page count is exactly one; inspect the rendered page for overflow, clipping and readable wrapping, and extracted text for reading order, missing characters and broken links. Compare formatting definitions with the starting source to confirm they are unchanged. If it spills onto another page, revise content and render again. If rendering is unavailable, report that one-page fit is unverified rather than declaring it checked.

No format guarantees an ATS score. Inspect the existing template’s text extraction without redesigning it. Use a requested alternative file type only while preserving the established appearance as closely as possible and checking the exported result.

## Saving final resumes

After successful compilation and the one-page, layout and content checks, save the finished PDF in `final-resumes/<Role>/` at the workspace root. Use the same role directory names as this rulebook. Name files `Aditya_Shriwas_<Role>_YYYY-MM-DD.pdf` using the user's local date; include the company or job identifier for job-specific versions. Preserve earlier finals by adding `_v2`, `_v3`, etc. for filename collisions. Keep drafts, logs and intermediate output in `build/`. Do not label an unverified or failed build as final. Link the saved final PDF when delivering the update.

## Keywords and selection

First separate target-JD mandatory qualifications, preferences and duties. Check years, graduation year, location, work authorization and stack gates before tailoring. Six months is not one year; internships or projects count only where the employer explicitly permits them.

Select keywords at the intersection of the target JD and credible candidate evidence. Use the employer's exact term once where natural, then demonstrate it in a concrete bullet. Treat alternatives as alternatives: React or Angular does not require both. Distinguish API consumption from API development, React from React Native, authentication from authorization, SQL from a specific database, and cloud-hosted use from infrastructure administration. Never hide keywords or repeat synonym lists.

## Projects, bullets and outcomes

Select two or three projects by role relevance, attributable depth, demonstrability and distinct engineering evidence. Prefer a real workflow and a specific contribution over a long stack list. Describe the user task the feature enables, implementation method where useful, and evidence of the result. Do not add every dependency.

Use **action, specific contribution, method/context and truthful outcome or user purpose** as ingredients, not a compulsory sentence template. Include what helps explain the contribution, in a natural order. A functional outcome such as saved drafts or caregiver communication is valid without a numerical gain. Do not promise outcomes not demonstrated: supporting incident reports does not mean reducing incidents; Keychain storage does not prove end-to-end security.

Preserve valid metrics with units, baseline, scope and measurement period. If metrics are missing, state the implemented behavior rather than inventing improvements, users, revenue, adoption, team size or time savings. Retain a real user benefit when shortening a bullet. Avoid unsupported “optimized,” “scalable,” “seamless,” “robust,” “production-grade” and “end-to-end” claims; explain the concrete mechanism and limits instead.

## Content-driven bullet length

**There is no fixed character or word limit.** Let the contribution determine the length. Bullets may differ in length when their substance differs; visual uniformity must not force repetitive phrasing or remove useful evidence.

Keep related details together around one coherent feature, problem or achievement. An interface workflow, its API integration and the resulting user capability can belong in the same bullet when they describe the same contribution. Include relevant technical specifics and outcomes where supported, but do not turn a bullet into a list of unrelated technologies or every task performed.

Use the shortest wording that fully explains the useful evidence. A simple achievement can be brief; a substantial workflow may need more detail. Split a bullet when it combines independent contributions or becomes difficult to scan. Never pad a short point or strip a meaningful detail solely to match the length of other points.

Vary openings and endings naturally. Do not force every bullet to start with the same verb or end with a generic benefit. Changing synonyms alone does not fix repeated content. Role-file examples illustrate possible claims, not prescribed lengths or sentence patterns.

Review the rendered resume for readability, line wrapping, dense paragraphs, overflow and balance. Tighten wording or prioritize content to keep exactly one page within the unchanged template; preserve genuine metrics, technical meaning and user benefits. No character-count validator or count report is required.

## Repetition and final review

Assign each bullet one main contribution. Compare action verbs, objects, user workflows, technologies and outcomes across all sections. Merge bullets that repeat the same API integration or component work. Repeated technology names are acceptable when needed for clarity, but each bullet must add evidence. Keep tense, spelling and terminology consistent.

For future updates: load the role file and this file; read the target JD; review the current resume, READMEs and relevant available code; mark verified/reported/inferred/unverified claims; tailor summary, skills and project order; preserve genuine impact; review every bullet for coherent detail and readability; audit repetition and professional-versus-project attribution. Deliver the revised content with a concise report of relevance, claim checks, repetition, unchanged formatting, verified one-page output, excluded unsupported keywords and remaining evidence gaps. Ask only for details that materially affect the actual proposed resume.
