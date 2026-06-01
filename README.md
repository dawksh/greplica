# Greplica

Greplica (`greplica`) stores lightweight codebase memory for coding agents. The CLI provides small graph-memory primitives; agent workflows are provided as skills.

## Agent Setup

Copy this prompt into the coding agent from the repository you want to use with Greplica:

````txt
Install Greplica for this repo.

Run these commands to install the CLI and fetch the skill folders:

```bash
set -euo pipefail

GREPLICA_REPO_URL="${GREPLICA_REPO_URL:-git@github.com:Autoloops/greplica.git}"
GREPLICA_TMP_DIR="$(mktemp -d)"

git clone --depth 1 "$GREPLICA_REPO_URL" "$GREPLICA_TMP_DIR"
npm install --prefix "$GREPLICA_TMP_DIR"
npm run --prefix "$GREPLICA_TMP_DIR" build
npm install -g "$GREPLICA_TMP_DIR"

printf "Skill folders:\n%s\n%s\n" "$GREPLICA_TMP_DIR/skills/greplica-bootstrap" "$GREPLICA_TMP_DIR/skills/greplica-update-working-memory"
greplica doctor --check-openai
```

Install these skills from the printed folders into your configured user-level skill directory using your native skill installation mechanism:

```txt
$GREPLICA_TMP_DIR/skills/greplica-bootstrap
$GREPLICA_TMP_DIR/skills/greplica-update-working-memory
```

If `greplica doctor --check-openai` reports that `OPENAI_API_KEY` is missing or invalid, stop and ask me to set it. Do not ask me to paste the key into chat. I can set it either in my shell before starting the coding agent, or in this repo's `.env.local` file:

```txt
OPENAI_API_KEY=...
```

After setup, tell me how to invoke the `greplica-bootstrap` and `greplica-update-working-memory` skills.
````

## Configuration

`greplica` looks for `OPENAI_API_KEY` in this order:

1. the process environment
2. `<repo-root>/.env.local`
3. `<repo-root>/.env`

The key is never printed by `greplica doctor`.

Memory is stored in `~/.greplica/graph.db` by default. Set `GREPLICA_HOME` only for tests or advanced isolated runs.

## Commands

```bash
greplica doctor [--check-openai]
greplica graph read
greplica graph context "<query>"
greplica proposal validate <proposal.json>
greplica proposal apply <proposal.json>
```

`greplica` automatically prepares repo memory state when commands run, so users should not need a separate init step.
