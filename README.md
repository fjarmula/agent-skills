# Agent Skills

A project-local skill pack for Claude Code and Codex. Both use the same
skill files in `.agent/skills/`. Claude Code uses `.claude/skills/` and
Codex uses `.agents/skills/`. Both paths are relative symlinks that a
project can commit and share with its team.

[Codex skill discovery documentation](https://learn.chatgpt.com/docs/build-skills)

## Install in a project

From the target project's root, set `pack` to the absolute path of this
repository and run the block. It checks for existing destinations before
copying the committed snapshot. The checkout must support symlinks.

```bash
pack="/absolute/path/to/agent-skills"
(
  set -e
  set -o pipefail
  for path in .agent/skills .claude/skills .agents/skills; do
    if [ -e "$path" ] || [ -L "$path" ]; then
      printf 'Already exists: %s\n' "$path" >&2
      exit 1
    fi
  done
  git -C "$pack" archive HEAD -- .agent/skills .claude/skills .agents/skills |
    tar -xf -
)
```

Start a new agent session in the target project. Keep its workflow rules
in its own `AGENTS.md` and `CLAUDE.md`. Commit the installed paths if
the team should share the same version. To install a specific release,
use its commit or tag in place of `HEAD`. For an existing installation,
review differences against the desired revision before updating the
tracked skills.

## Optional integrations

Some skills expect additional tools. See each skill's `SKILL.md` for its
requirements. The [Claude Code](examples/claude-mcp.json) and
[Codex](examples/codex-mcp.toml) examples show the Graphify and Context7
MCP registrations from GreenWave. Merge the entries you want into
`.mcp.json` or `.codex/config.toml`. The Graphify example expects
`graphify-mcp` and a graph generated in the target project. The Context7
example uses `npx`. The Playwright skill uses its CLI.

## Provenance

The 32 skill directories are the tracked GreenWave snapshot identified
below, including the upstream source records in their `.openskills.json`
files. The upstream projects are Superpowers, Emil's Skills, Caveman,
Graphify, Context7, and Playwright CLI. Check the respective upstream
terms before redistributing the snapshot.

GreenWave snapshot `eb2ede66db809a4cd0d5839418f7938af5224211`.
