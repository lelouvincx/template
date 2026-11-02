# Project instructions

- Keep shared agent infrastructure in `lelouvincx/agent-skills`. Run `.agents/sync` after changing runtime customizations.
- Put project customizations and application lifecycle work in `.agents/project`. Keep sync free of application work.
- Run `bash tests/lifecycle.sh` and `bash tests/configure-amp.sh` before delivery. Add stack-specific checks when the project gains application code.
- Use conventional branches and commits. Read `.github/pull_request_template.md` before opening a pull request.
- For shipping, load `shipping-personal-projects` and preserve project-specific release gates. Use the approved bot publisher. Stop if authorization or the approved publishing path is unavailable.
- Add a linked `CHANGELOG.md` entry after opening each pull request, in a separate commit.
- Keep secrets in 1Password. Local env files contain `op://` references only; resolve secrets at execution time.
- Store transient evidence under `.amp/in/artifacts/`. Preserve unrelated work and runtime files.
