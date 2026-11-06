# Agent Skills

A project-local skill pack for Claude Code and Codex. Both use the same
skill files in `.agent/skills/`. Claude Code uses `.claude/skills/` and
Codex uses `.agents/skills/`. Both paths are relative symlinks that a
project can commit and share with its team.

[Codex skill discovery documentation](https://learn.chatgpt.com/docs/build-skills)

## Install in a project

### 1. Clone the skills repo

Choose where to keep your copy of the skills repo. This example puts it in
`~/Documents/projects/agent-skills`.

```bash
mkdir -p "$HOME/Documents/projects"
git clone https://github.com/fjarmula/agent-skills.git "$HOME/Documents/projects/agent-skills"
```

If you already have a local clone, skip this step and use its path in step 2.

### 2. Run the install block from your target project

Change into the project that should get the skills. Replace
`/absolute/path/to/your-project` with that project's path.

```bash
cd "/absolute/path/to/your-project"
```

Then run this block from there. Set `pack` to the absolute path of the
**skills repo clone**. The example uses the path from step 1.

```bash
pack="$HOME/Documents/projects/agent-skills"
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

Git reads the committed files from the clone at `pack`. The block extracts
them into your **current directory**, so the agents read local copies after
installation. It stops before copying if any of the three skill paths already
exists. If that happens, review the existing skills before merging them.
Your filesystem needs to support symlinks.

### 3. Start a new agent session

Open the target project in Claude Code or Codex. If teammates should get the
same setup, commit `.agent/skills`, `.claude/skills`, and `.agents/skills` in
the target project.

To install a specific version in a fresh project, replace `HEAD` in the block
with a commit or tag from the clone. Installed copies update independently of
GitHub. When updating an existing installation, review the differences before
replacing any skills.

## Optional integrations

Some skills expect additional tools. See each skill's `SKILL.md` for its
requirements. The [Claude Code](examples/claude-mcp.json) and
[Codex](examples/codex-mcp.toml) examples show how to configure Graphify and
Context7. Merge the entries you want into
`.mcp.json` or `.codex/config.toml`. The Graphify example expects
`graphify-mcp` and a graph generated in the target project. The Context7
example uses `npx`. The Playwright skill uses its CLI.

## Licenses

The install includes the [third-party license notices](.agent/skills/.licenses/THIRD_PARTY_NOTICES.md).
The MCP configuration examples use the [MIT license](examples/LICENSE).
