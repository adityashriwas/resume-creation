# resume-creation

LaTeX resume workspace for Aditya Shriwas, with role-specific guidance and project evidence for future updates.

- `main.tex`: editable resume source.
- `Aditya_Shriwas_Resume.pdf`: original resume reference.
- `resume-rulebook/`: shared rules, role guides, and candidate evidence.
- `projects/`: supporting project documentation.
- `final-resumes/`: approved, checked final resumes grouped by role.
- `AGENTS.md`: instructions for resume updates.

## Build and live preview

Install [Tectonic](https://tectonic-typesetting.github.io/) and ensure `tectonic` is on your PATH. On macOS with Homebrew, run `brew install tectonic`. Build from the project root:

```sh
mkdir -p build
tectonic --synctex --keep-logs --outdir build main.tex
```

The preview is written to `build/main.pdf`. Temporary build files are excluded from Git. VS Code settings in `.vscode/` configure automatic builds and PDF preview with LaTeX Workshop; adjust the Tectonic executable path if needed.

Preserve the current template and keep the resume on one page. Drafts stay in `build/`; save final versions in `final-resumes/` only after approval.

## Instructions for the next agent

1. Read [AGENTS.md](AGENTS.md) before editing. Follow the user's latest instructions if they change earlier preferences. Treat the current `main.tex` as the formatting baseline, including manual edits; do not replace it with an older draft or PDF.
2. Read [the rulebook overview](resume-rulebook/README.md), [shared resume rules](resume-rulebook/SHARED_RESUME_RULES.md), and the matching role guide below. Use the actual job description when supplied. Do not create another role variant unless requested.
3. Review [candidate evidence](resume-rulebook/CANDIDATE_EVIDENCE.md), the current resume, the original PDF, and relevant documentation in `projects/`. Distinguish user-reported contributions from documented project capabilities and unverified authorship. Application source code is not included here; do not imply that documentation alone verifies authorship.
4. Edit resume content in `main.tex`. Preserve fonts, margins, headings, spacing, alignment, section order, and existing bold emphasis. Fit the content by improving wording and removing repetition, never by changing the template. Bullet lengths follow the contribution, with no fixed character limit.
5. Compile into `build/`, check the log for errors and overflow, and inspect the rendered PDF. Confirm exactly one page, readable text, correct emphasis, working links, and sensible text extraction order. If rendering cannot be checked, report page fit as unverified and do not label the output final.
6. Share the draft PDF with a concise explanation of changes, evidence gaps, and layout checks. Keep it in `build/` until the user confirms finalization. Compilation alone is not approval.
7. After approval and successful checks, copy the PDF into the matching `final-resumes/<Role>/` folder. Use `Aditya_Shriwas_<Role>_YYYY-MM-DD.pdf` with the user's local date (Asia/Kolkata unless updated). Add a company/job identifier for targeted versions and `_v2`, `_v3`, etc. for collisions. Preserve previous finals and link the saved PDF in the response.

| Role / output folder | Guide |
| --- | --- |
| `SDE` | [SDE instructions](resume-rulebook/SDE/RESUME_INSTRUCTIONS.md) |
| `Software_Developer` | [Software Developer / Engineer instructions](resume-rulebook/Software_Developer/RESUME_INSTRUCTIONS.md) |
| `Full_Stack_Developer` | [Full Stack instructions](resume-rulebook/Full_Stack_Developer/RESUME_INSTRUCTIONS.md) |
| `Backend_Developer` | [Backend instructions](resume-rulebook/Backend_Developer/RESUME_INSTRUCTIONS.md) |
| `Frontend_Developer` | [Frontend instructions](resume-rulebook/Frontend_Developer/RESUME_INSTRUCTIONS.md) |
| `Mobile_Application_Developer` | [Mobile instructions](resume-rulebook/Mobile_Application_Developer/RESUME_INSTRUCTIONS.md) |

### Content constraints to retain

- Keep the summary focused on the role's responsibilities and competencies, without an experience month count. For general SDE roles, keep technology names in skills and project evidence rather than the summary.
- Include ExpressU, TaleCrafter AI, and E-Learning in the consolidated resume; omit SafetyBriefer and create-mern-api unless the user changes that direction. Keep ExpressU first for the SDE version.
- Preserve TaleCrafter's confirmed real-user usage without inventing a count or using estimated engagement figures as measured results. E-Learning was a complete project; maintenance work at the employer involved requested features in existing applications, not original ownership of those applications.
- Do not convert personal backend project work into professional backend experience, or mobile API integration into backend ownership. Do not invent productivity metrics for AI-assisted work.
- Use hyphen markers for project points. Keep the personal GitHub profile link, omit project repository links, and preserve the two-line contact/social header with full profile paths.

## Repository conventions

The `.gitkeep` files preserve empty role folders in Git. They are placeholders, not completed resumes, and may remain alongside future PDFs. Generated previews, logs, and other temporary files belong in the ignored `build/` directory, which the build command recreates after cloning. Keep approved final PDFs and the original reference PDF tracked.

For any resume edit, consult `AGENTS.md` and the rulebook for the full requirements; this README is an entry point rather than a replacement for them.
