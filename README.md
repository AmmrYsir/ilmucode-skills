# ILMUcode Skills Collection

A curated collection of [ILMUcode](https://ilmucode.com) skills — reusable instruction packs that teach the ILMUcode agent how to perform specialized tasks on your machine.

## What is a skill?

A skill is a folder containing a `SKILL.md` file with YAML frontmatter (`name`, `description`) and a markdown body of instructions. ILMUcode keeps every installed skill's *name and description* in context at all times, and loads the full instructions only when a task matches — so skills extend what the agent can do without bloating every conversation.

This collection currently contains:

| Skill | What it does |
|-------|--------------|
| [`rea-reversing`](skills/rea-reversing/) | Guides the agent through using the installed `rea` CLI to reverse engineer binaries, APK/IPA apps, Electron/JavaScript apps, .NET assemblies, firmware, WASM, model files, and HAR/pcap captures |
| [`roblox-game`](skills/roblox-game/) | Expert Roblox game development companion — Luau patterns, genre scaffolds (Simulator, Tycoon, Obby, RPG, Horror, Battle Royale), DataStore persistence, monetization, security hardening, performance audits, and Roblox Studio MCP integration |

## Installing a skill

Skills only take effect when they live in a location ILMUcode discovers. There are two, checked in this priority order:

1. `<project>/.ilmu/skills/<name>/SKILL.md` — project, highest priority (use to *override*)
2. `<project>/.agents/skills/<name>/SKILL.md` — project
3. `~/.ilmu/skills/<name>/SKILL.md` — user (overrides user-level `.agents`)
4. `~/.agents/skills/<name>/SKILL.md` — user, standard location

### Option A — user-based (recommended for personal, all-projects use)

Copy the skill folder into your home directory so it's available in **every** project:

```bash
# Git Bash / Linux / macOS
mkdir -p ~/.agents/skills
cp -r /path/to/this/repo/skills/rea-reversing ~/.agents/skills/
```

```powershell
# Windows PowerShell
New-Item -ItemType Directory -Force "$HOME\.agents\skills" | Out-Null
Copy-Item -Recurse .\skills\rea-reversing "$HOME\.agents\skills\"
```

### Option B — project-based (for sharing with a team via git)

Copy it into the project that needs it, and commit it so teammates get it when they pull:

```bash
cd /path/to/your-project
mkdir -p .agents/skills
cp -r /path/to/this/repo/skills/rea-reversing .agents/skills/
git add .agents/skills && git commit -m "Add rea-reversing skill"
```

### Option C — clone directly into a skills directory

Skip copying entirely by cloning this repo and linking or moving folders where you want them:

```bash
git clone https://github.com/<you>/<this-repo>.git
mv <this-repo>/skills/rea-reversing ~/.agents/skills/
```

### Notes

- The skill **folder name must match** the `name` in its `SKILL.md` frontmatter (e.g. `rea-reversing/SKILL.md` must declare `name: rea-reversing`).
- If the same skill name exists in both `~/.agents/skills` and `<project>/.ilmu/skills`, the project copy wins — use this to customize a shared skill per project.
- After installing, start a fresh ILMUcode session; the skill's description is picked up automatically, or invoke it explicitly with `/rea-reversing`.
- Skills that rely on external tools (like `rea-reversing`, which needs the [`rea` CLI](https://github.com/morluto/rea)) additionally require that tool to be installed on your system — run `rea doctor` to check. `roblox-game` works offline out of the box, and gains live Studio execution when a Roblox Studio MCP server is configured.

## Contributing

Have a workflow you keep repeating with ILMUcode? Turn it into a skill:

1. Create `skills/<your-skill-name>/SKILL.md` with `name` (kebab-case, matching the folder) and a pushy `description` that says *when* the skill should trigger.
2. Keep the body under ~500 lines; move large reference material into a `references/` subfolder and point to it.
3. Test it in a fresh session with a realistic prompt, and iterate.

PRs welcome.
