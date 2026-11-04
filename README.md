# resume-creation

LaTeX resume workspace for Aditya Shriwas, with role-specific guidance and project evidence for future updates.

- `main.tex`: editable resume source.
- `Aditya_Shriwas_Resume.pdf`: original resume reference.
- `resume-rulebook/`: shared rules, role guides, and candidate evidence.
- `projects/`: supporting project documentation.
- `final-resumes/`: approved, checked final resumes grouped by role.
- `AGENTS.md`: instructions for resume updates.

Build with Tectonic from the project root:

```sh
mkdir -p build
tectonic --synctex --keep-logs --outdir build main.tex
```

The preview is written to `build/main.pdf`. Temporary build files are excluded from Git. VS Code settings in `.vscode/` configure automatic builds and PDF preview with LaTeX Workshop; adjust the Tectonic executable path if needed.

Preserve the current template and keep the resume on one page. Drafts stay in `build/`; save final versions in `final-resumes/` only after approval.
