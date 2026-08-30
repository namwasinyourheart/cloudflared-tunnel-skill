# cloudflared-tunnel skill

Agent skill to create a **quick tunnel** with `cloudflared tunnel --url` that
exposes a local web server/port to the internet through Cloudflare — no account,
no DNS required.

Written in the open, multi-agent [Agent Skills](https://agentskills.io) format —
works with OpenCode, Claude Code, Codex, Cursor and many other agents.

## Structure

```
skills/
└── cloudflared-tunnel/
    └── SKILL.md
```

## Installation

### Option 1: `skills` CLI (recommended — install over the internet)

```bash
# Preview / list
npx skills add namwasinyourheart/cloudflared-tunnel-skill --list

# Install the skill
npx skills add namwasinyourheart/cloudflared-tunnel-skill --skill cloudflared-tunnel
```

Add `-g` for a global install and `-a opencode` / `-a claude-code` to target a
specific agent:
```bash
npx skills add namwasinyourheart/cloudflared-tunnel-skill --skill cloudflared-tunnel -a opencode -g
```

### Option 2: OpenCode — clone + point `skills.paths`

```bash
git clone https://github.com/namwasinyourheart/cloudflared-tunnel-skill.git
```

Then in `opencode.json`:
```json
{
  "$schema": "https://opencode.ai/config.json",
  "skills": { "paths": ["/path/to/cloudflared-tunnel-skill/skills"] }
}
```

After configuring, **quit and restart opencode** for the skill to load.

### Option 3: manual copy (cross-compatible)

Copy the skill directory into the agent's expected path:
```bash
cp -r skills/cloudflared-tunnel ~/.config/opencode/skills/          # opencode (global)
cp -r skills/cloudflared-tunnel ~/.claude/skills/                   # claude-code (global)
cp -r skills/cloudflared-tunnel .opencode/skills/                   # opencode (project)
```

## Skill contents

SKILL.md covers:
- Installing `cloudflared` (macOS/Linux/Windows)
- Running `cloudflared tunnel --url http://localhost:PORT` and grabbing the `*.trycloudflare.com` URL
- Common options (`--logfile`, `--no-autoupdate`, `--loglevel`)
- Notes on temporary tunnels, 502 errors, and security when exposing a server

## License

MIT
