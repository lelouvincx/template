# Personal-project starting point

Generate a repository from this template, then clone that generated repository. Choose your application stack separately.
This template includes no application, deployment, credentials or copied infrastructure installers.

## Start a generated project

1. Confirm `git remote get-url origin` points to the generated repository, not the template.
2. Install Bash and Git. Install jq and authenticate the supported Amp CLI if you want an Amp project.
3. Run `.agents/setup`. Shared infrastructure may install its own dependencies and project the agent runtime.
4. Add project commands to `.agents/project`, then replace this introduction with your project's purpose and application checks.
5. For a `lelouvincx`-owned GitHub repository, explicitly run `.agents/configure-amp` to configure its personal Amp mapping.

The configuration command is optional and changes remote Amp settings. It does not run during setup, resume, sync or CI.
It requires an already-authenticated Amp CLI and never resolves credentials or grants GitHub access.
It works before the generated repository has its first commit, and leaves the default branch unset.
The owner must separately authorize bot publishing and effective GitHub permissions before shipping.

## Shared source and lifecycle

Source selection uses the first applicable path:

1. nonempty `AGENT_SKILLS_DIR`;
2. existing `/home/user/workspace/repos/agent-skills`;
3. existing sibling `agent-skills`;
4. `/home/user/workspace/agent-skills`.

Missing source is cloned from `AGENT_SKILLS_REPO` (default: `https://github.com/lelouvincx/agent-skills.git`).
An invalid selected path fails, including symlinks, non-checkouts and checkout subdirectories. Linked Git worktree roots are supported.
Canonical shared hooks own refresh and projection: clean sources fast-forward; dirty sources remain untouched.
Use an absolute `AGENT_SKILLS_DIR` for a different local location. No consumer hook pulls source itself.

| Command | Shared baseline | Project stage |
| --- | --- | --- |
| `.agents/setup` | canonical setup | common customizations, then app setup |
| `.agents/resume` | canonical resume | common customizations, then app resume |
| `.agents/sync` | canonical resume | common customizations only |

The common section in `.agents/project` runs after every successful baseline projection.
Make customizations idempotent, preserve unrelated runtime files and fail explicitly.
Removing an override restores the baseline on the next projection. Explicitly remove obsolete files only your project created.
Sync must never install an application, migrate or seed a database, or start services.

## Amp configuration boundary

`.agents/configure-amp` derives repository identity from standard GitHub HTTPS or SSH `origin` URLs.
It finds the exact personal mapping by repository URL, checks user ownership and records the ID in local Git config immediately after creation.
Ambiguous maps, custom SSH hosts, mismatched stored IDs and another owner's repository fail closed.
The command uses only `amp projects` CLI commands. It applies the selected shared source's canonical pre-setup and Ship prompt.
It sets GitHub mode, thread-creator authorship, its own standard SSH alias, and empty base/extra-repository/script/Store settings.
Name, icon, orb size, ownership and other unmanaged fields stay unchanged.

Amp's current `get` JSON omits Ship settings and additional repositories. Exposed fields and gallery are read back.
A local apply receipt avoids repeated writes when desired inputs and exposed fields match; it cannot detect drift in omitted fields.
Verify those fields in Amp. To deliberately reapply after hidden drift, remove only the receipt with
`git config --local --unset agentInfrastructure.ampReceipt`, then rerun the command.
The ID remains recorded after partial failure so a retry cannot silently create another project.

## Check changes

```bash
for file in .agents/* tests/*.sh; do bash -n "$file"; done
bash tests/lifecycle.sh
bash tests/configure-amp.sh
```

Tests use generated copies, temporary HOME directories, local Git and a mocked Amp CLI. They do not install or deploy anything.
For real shared-runtime validation, explicitly select a trusted source and isolate HOME and AMP_CONFIG_DIR before projection.
Add stack-specific tests and release gates when adding application code. Never store plaintext secrets in env or credential files.
