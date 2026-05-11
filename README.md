# organize-folder

A Claude Code skill that turns a cluttered Downloads or Documents folder into a clean, documented structure — without moving a single file until you approve.

## What it does

1. **Scans** your folder and summarizes what's there
2. **Asks 4-5 questions** to understand how you think about your files (not how a filing system thinks)
3. **Proposes a folder tree** with no overlapping categories — you approve before anything moves
4. **Executes** the reorganization, trashing duplicates if you want
5. **Writes a `CLAUDE.md`** so future-you (or future-Claude) knows the rules

## Install

```bash
claude plugin marketplace add brayansdt/organize-folder-skill
claude plugin install organize-folder@organize-folder-skill
```

Then in any Claude Code session:

> "organize my Downloads"  
> "my Documents folder is a mess"  
> "help me sort this folder"

Claude will invoke the skill automatically.

## Example output structure

For a freelancer's folder:

```
finance/
  personal/       ← pay slips, tax returns
  clients/        ← invoices per client
  tax/            ← year bundles for accountant (2024/, 2024-25/)
work/             ← job docs, notes, presentations
personal-docs/    ← passport, ID, certificates
media/            ← photos, videos
misc/             ← unclear files, review quarterly
```

For a developer's Downloads:

```
dev/              ← code, JSON, CSVs, exports
docs/             ← PDFs, guides, specs
media/            ← screenshots, images
installers/       ← DMG, PKG, ZIP apps
misc/             ← unclear files
```

The structure is always tailored to what's actually in your folder — not a template.

## Key behaviors

- **Never moves anything without approval** — Phase 3 (proposal) is mandatory
- **Detects finance docs** and asks about personal vs business split if relevant
- **Handles duplicates** — files like `report (1).pdf` where the original exists
- **Cross-cutting buckets** — if you say "keep all client X files together", it respects that over topic
- **Writes CLAUDE.md** at the folder root documenting your specific rules for next time

## Requirements

- Claude Code
- macOS, Linux, or Windows (uses `mv` and `ls`)
