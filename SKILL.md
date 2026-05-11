---
name: organize-folder
version: 1.0.0
description: |
  Organize a messy Downloads, Documents, or any folder. Scans contents, asks a few
  targeted questions to understand the user's mental model, proposes a clean
  non-overlapping folder structure, executes the reorganization, and writes a CLAUDE.md
  for future reference. Use when the user says "organize my Downloads", "clean up my
  folder", "sort this mess", "tidy up my files", "help me organize this", or shows you
  a folder full of unsorted files. Proactively suggest when the user mentions their
  Downloads is cluttered, they can't find files, or they want to move files to Documents.
allowed-tools:
  - Bash
  - Read
  - Write
  - Edit
  - AskUserQuestion
---

# Organize Folder

Turn a cluttered folder into a clean, documented structure in 5 phases.
Never move or delete anything until the user approves the proposed structure.

---

## Phase 1: Scan

Run `ls -la <folder>` and count total items. Identify rough clusters by scanning filenames and extensions:

- **Documents**: PDFs, DOCXs, spreadsheets, text files
- **Media**: images, videos, audio
- **Code/Dev**: JS, JSON, CSV, ZIPs, HTML, config files
- **Installers**: DMG, PKG, EXE, APP
- **Archives**: ZIP, TAR, GZ (non-installer)

Report a one-paragraph summary: total count, rough breakdown, any obvious patterns (e.g. "lots of invoices", "many screenshots", "several folders already exist").

---

## Phase 2: Ask Clarifying Questions (one at a time)

Ask these in order. Wait for each answer before asking the next.

**Q1 — Primary axis**
How do you mentally reach for a file — by *what it's about* (topic), *who it belongs to* (person), or *when it's from* (time)?

**Q2 — Cross-cutting buckets**
Are there any categories that override topic — like all files for a specific client, country, language, or project that you'd rather keep together regardless of file type?

**Q3 — Finance split** *(ask only if finance/invoice/tax/receipt files detected)*
Finance docs — do you want a single `finance/` folder, or split into personal vs business (freelance clients, investments, a company, etc.)?

**Q4 — Unidentifiable files**
For files with UUID names, gibberish, or that I can't confidently categorize: put them in a `misc/` holding pen, leave them in place, or would you rather I ask about each one?

**Q5 — Duplicates**
Files like `report (1).pdf` where `report.pdf` also exists — trash the duplicate, or keep both?

Adapt the questions to what's actually in the folder. Skip Q3 if there are no finance-related files. Skip Q5 if there are no obvious duplicates.

---

## Phase 3: Propose Structure

Present a folder tree with one-line annotations. Example format:

```
finance/
  personal/       ← pay slips, tax returns, personal invoices
  business/       ← client invoices, contracts (name this after their context)
  tax/            ← year-based bundles for accountant (2024/, 2024-25/, etc.)
work/             ← job-related docs, notes, presentations
media/            ← photos, videos, audio
dev/              ← code, exports, JSON, CSVs, scripts
personal-docs/    ← ID, passport, certificates
installers/       ← DMG, PKG, ZIP app installers
misc/             ← unclear files, review quarterly
```

**Overlap check**: before presenting, verify no two folders would receive the same file type. Flag any ambiguous cases explicitly ("invoices from clients — finance/business/ or work/?") and recommend a rule.

**Key structural patterns to look for:**
- If a domain has both *one-time reference docs* (contracts, reports, inspections) AND *recurring docs* (statements, invoices, receipts) — split them. They have different retrieval patterns.
- If tax files exist, propose `tax/` as a sibling under `finance/` (not nested in personal) — year-based bundles are easier to hand to an accountant.
- If a cross-cutting bucket was named in Q2, make it top-level regardless of what's inside.

Ask: "Does this look right? Any folders to rename, add, or merge?"
Wait for approval before proceeding.

---

## Phase 4: Execute

Work in batches by destination folder. For each batch:

1. Create the folder
2. Move files with `mv`, echoing each filename
3. Report the count moved

**Duplicate handling** (if user said trash):
Only trash a file with ` (1)` / ` (2)` in the name if the original (without the suffix) exists in the same location. Move to `~/.Trash/`. Never trash without confirming the original exists.

**Unidentifiable files**: move to `misc/` and list them at the end so the user can review.

**Existing subfolders**: move them as units into the appropriate parent — don't flatten their contents unless the user asked for it.

After all moves, print a final summary:
```
Done. X files moved across Y folders. Z files in misc/ to review.
```

---

## Phase 5: Write CLAUDE.md

Place a `CLAUDE.md` at the root of the organized folder. Tailor it to the exact structure created — don't use boilerplate.

Include:

```markdown
# Folder Organization

## Structure
[folder tree with annotations matching what was actually created]

## Key Rules
- [what goes where, based on decisions made]
- [what NOT to mix — the most common confusion points]
- misc/ is a holding pen. Review and clear quarterly.

## Adding a New [X]
[1-3 steps for the most likely future additions based on what's in the folder]
```

Keep it short. The goal is that future-Claude (or the user) can reopen this folder months later and immediately understand the system.

---

## Principles

- **Never move anything before approval.** Phase 3 is mandatory.
- **Propose, don't impose.** The user's mental model beats any "correct" taxonomy.
- **No over-engineering.** Three similar files don't need a subfolder. A dozen do.
- **misc/ exists for a reason.** Don't force bad categorizations to avoid it.
- **Reference vs recurring.** Contracts and reports (read once, keep forever) belong separate from invoices and statements (accumulate over time, tax-relevant).
