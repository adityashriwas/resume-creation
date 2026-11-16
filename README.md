# Resume Creation

A reusable LaTeX resume template with role-specific writing guides and VS Code preview settings. Public files contain placeholders; each person's resume and evidence stay in the Git-ignored `local/` folder.

## Requirements

- VS Code (or an editor of your choice).
- Tectonic, available on your command-line PATH. Install from the [Tectonic project](https://tectonic-typesetting.github.io/). On macOS with Homebrew: `brew install tectonic`. For Windows/Linux use the project's installation instructions or platform release binary, then add its directory to PATH.
- VS Code extension **LaTeX Workshop**, ID `james-yu.latex-workshop`.

Check installation with `tectonic --version`. Restart VS Code after changing PATH. Tectonic may need internet access on the first build to download fonts and packages; a first build can take several minutes. The configured recipe uses Tectonic to manage its TeX dependencies.

## Windows laptop setup (PowerShell)

Use PowerShell and native Windows tools for the steps below.

1. Install [Visual Studio Code](https://code.visualstudio.com/) and [Git for Windows](https://git-scm.com/downloads/win). Git is needed for cloning and pushing; you can also download and extract the repository ZIP for local editing.
2. Follow the [official Tectonic installation guide](https://tectonic-typesetting.github.io/book/latest/installation/). For a manual installation, download the Windows release archive matching your system from [Tectonic releases](https://github.com/tectonic-typesetting/tectonic/releases), extract it, and place `tectonic.exe` in a permanent folder such as `C:\Tools\Tectonic`.
3. Add the **folder containing `tectonic.exe`** to your user PATH: search Windows for **Edit environment variables for your account**, select **Path → Edit → New**, enter `C:\Tools\Tectonic`, and save. Fully close and reopen VS Code and PowerShell.
4. Verify in a new PowerShell window:

   ```powershell
   git --version
   Get-Command tectonic
   tectonic --version
   ```

5. Clone your repository (replace the example URL), or open your extracted ZIP folder:

   ```powershell
   git clone https://github.com/YOUR-USERNAME/YOUR-REPOSITORY.git
   Set-Location YOUR-REPOSITORY
   code .
   ```

   You can also launch VS Code and use **File → Open Folder**. Select the repository folder containing both `main.tex` and `.vscode`.
6. Press **Ctrl+Shift+X**, search for **LaTeX Workshop**, and install the extension by **James Yu** (`james-yu.latex-workshop`). The repository also recommends it through `@recommended`.
7. Open **Terminal → New Terminal** and select **PowerShell**. Follow the private-copy setup below, then edit `local/main.tex`.
8. Save with **Ctrl+S**. Press **Ctrl+Shift+P**, run **LaTeX Workshop: Build LaTeX project**, then run **LaTeX Workshop: View LaTeX PDF file**. To rebuild automatically as you edit, enable the live-preview settings below.

The shared configuration uses the portable `tectonic` command from PATH. If you must set an absolute Windows path in JSON, use forward slashes (`"command": "C:/Tools/Tectonic/tectonic.exe"`) or escaped backslashes. Keep machine-specific configuration local and retain `tectonic` in the shared configuration.

## First-time setup

1. Clone your copy of this repository and open the **repository folder** in VS Code.
2. Open Extensions and search `@recommended`, then install LaTeX Workshop. Alternatively run `code --install-extension james-yu.latex-workshop` if the VS Code CLI is installed.
3. Create a private working copy. In macOS/Linux Terminal or Git Bash:

   ```sh
   mkdir -p local/build local/projects local/final-resumes
   cp main.tex local/main.tex
   cp resume-rulebook/CANDIDATE_EVIDENCE.md local/CANDIDATE_EVIDENCE.md
   ```

   In Windows PowerShell:

   ```powershell
   New-Item -ItemType Directory -Force local/build, local/projects, local/final-resumes
   Copy-Item main.tex local/main.tex
   Copy-Item resume-rulebook/CANDIDATE_EVIDENCE.md local/CANDIDATE_EVIDENCE.md
   ```

   For a new setup, run the copy commands above. For an existing setup, continue editing your current `local/main.tex` to preserve your work.
4. Fill in `local/main.tex` with your own information, including the `pdftitle` and `pdfauthor` metadata near the top. Keep the public `main.tex` anonymous.
5. Record supported contributions and preferences in `local/CANDIDATE_EVIDENCE.md`. Put project notes, original PDFs, job descriptions and reference resumes under `local/`.

## Editor settings and preview

The checked-in `.vscode/settings.json` configures:

| Setting | Default / purpose |
| --- | --- |
| Build tool | `tectonic` from PATH |
| Recipe | Tectonic, with SyncTeX and logs |
| Output directory | `build/` beside the edited LaTeX document |
| Auto-save | Off |
| Automatic build | Manual; run the build command when ready |
| PDF viewer | VS Code tab, right editor group, page-fit, original PDF colors |

To build, open **`local/main.tex`**, then use the Command Palette (`Cmd+Shift+P` on macOS, `Ctrl+Shift+P` on Windows/Linux) and select **LaTeX Workshop: Build LaTeX project**. Use **LaTeX Workshop: View LaTeX PDF file** to open the preview. Output should be `local/build/main.pdf`.

For a live preview, set these values in workspace Settings JSON:

```json
{
  "files.autoSave": "afterDelay",
  "files.autoSaveDelay": 1000,
  "latex-workshop.latex.autoBuild.run": "onFileChange"
}
```

Merge these keys into the existing settings, preserving the other entries. For content-only work, use manual builds. To resolve the Tectonic executable, verify PATH or configure its local path as described above.

### Build from a terminal

From the repository root:

```sh
mkdir -p local/build
tectonic --synctex --keep-logs --outdir local/build local/main.tex
```

On Windows PowerShell, from the repository root:

```powershell
New-Item -ItemType Directory -Force .\local\build
tectonic --synctex --keep-logs --outdir .\local\build .\local\main.tex
Start-Process .\local\build\main.pdf
```

The last command opens the PDF with your default Windows PDF application; use LaTeX Workshop's viewer for a preview inside VS Code. To build the anonymous template instead, use `build` as the output directory and `main.tex` as the input.

## Tailor a resume yourself or with an agent

1. Read [AGENTS.md](AGENTS.md), [the rulebook](resume-rulebook/README.md), and [shared rules](resume-rulebook/SHARED_RESUME_RULES.md).
2. Select a guide: `SDE`, `Software_Developer`, `Full_Stack_Developer`, `Backend_Developer`, `Frontend_Developer`, or `Mobile_Application_Developer`.
3. Provide your actual target job description and private candidate evidence. Distinguish employment from personal projects and your contributions from the team's work.
4. Edit `local/main.tex`, tailoring the summary, skills and contributions. Preserve formatting and describe supported user benefits and measured outcomes in concise, natural language.
5. If building is authorized, compile and inspect the current PDF for one-page fit, readable wrapping, bold text, links, overflow and text extraction order. Fit the page by tightening content while preserving the template. Follow compilation with a visual layout check.
6. After approval, save a checked final under `local/final-resumes/<Role>/Name_Role_YYYY-MM-DD.pdf`. Use your local date and `_v2`, `_v3`, etc. to preserve earlier versions. Keep drafts separate from approved finals.

Example agent request:

> Read AGENTS.md and the mobile developer guide. Use local/CANDIDATE_EVIDENCE.md and local/projects/ to tailor local/main.tex for the job description in local/job-description.txt. Preserve my format, use only supported claims, and update content only. Keep this step limited to source edits and share the changes for review. Building, finalization, and publishing will follow my approval.

## Folder layout and privacy

- `main.tex`: anonymous starter template.
- `resume-rulebook/`: public writing guidance and blank evidence worksheet.
- `projects/`: public instructions for collecting private project evidence.
- `.vscode/`: shared editor settings and extension recommendation.
- `local/`: ignored personal source, evidence, reference material and finals. **Back up this folder separately to preserve your personal files.**
- `build/`: ignored temporary output.
- `final-resumes/`: public role-folder placeholders only; personal PDFs are ignored.

The `.gitignore` rules keep PDFs, local data, build outputs and common LaTeX intermediates local. Store personal content under `local/`, keep tracked documents anonymous, and stage public template files explicitly. Git continues tracking changes to files already in its index, so review those changes before publishing.

Before publishing, run `git status --short`, inspect `git diff` and `git diff --cached`, and confirm `git check-ignore local/main.tex` reports the local file as ignored.

**Existing history:** Earlier GitHub commits, clones and forks retain their original content. For a clean reusable publication, create a new directory, copy the public template files into it, initialize a fresh repository, and publish its clean history. Keep the existing `.git`, `local/` and `build/` directories in the original workspace. Coordinate history cleanup separately when retaining an existing remote.

## Troubleshooting

- **Locate the build tool:** install Tectonic, check `tectonic --version`, and restart VS Code after updating PATH.
- **Configure Tectonic on Windows:** run `Get-Command tectonic`. Check that the extracted executable is directly inside the folder added to PATH, and reopen all VS Code windows after changing PATH. For an executable path containing spaces, PowerShell requires the call operator: `& "C:/Path With Spaces/tectonic.exe" --version`.
- **First build seems slow:** inspect the LaTeX Workshop output/logs for package downloads; check network access.
- **Open the current preview:** select `local/main.tex` as the active root and open `local/build/main.pdf`. Check the timestamp to confirm the PDF reflects the latest build.
- **LaTeX syntax errors:** escape literal special characters such as `&`, `%`, `_`, and `#` in text (`\&`, `\%`, `\_`, `\#`). Inspect the first error in the build log.
- **More than one page:** remove repetition and prioritize relevant supported content. Preserve the current fonts and margins.
