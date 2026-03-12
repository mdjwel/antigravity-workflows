---
description: Write accurate, end-user-centric changelogs by comparing versions via git history and browser exploration. Ensures entries are user-friendly, non-alarming, and free of developer jargon.
---

# Generate Changelog

This workflow generates changelog entries for a WordPress plugin based on git history. It supports two modes:

- **With a commit ID:** Generates entries from all commits since that commit, plus any uncommitted changes.
- **Without a commit ID:** Generates entries from the current uncommitted (staged + unstaged) changes only.

---

## Step 1 — Gather Changes

### Mode A: Commit ID provided

1. **Get the git log** since the provided commit ID:

   ```bash
   git log <COMMIT_ID>..HEAD --oneline --no-merges
   ```

2. **Get detailed commit messages** for more context:

   ```bash
   git log <COMMIT_ID>..HEAD --format="%h %s%n%b---" --no-merges
   ```

3. **Get the diff stat** to understand which files were changed:

   ```bash
   git diff <COMMIT_ID>..HEAD --stat
   ```

4. **Check for uncommitted changes** that should also be included:

   ```bash
   git diff HEAD --stat
   ```

### Mode B: No commit ID provided (uncommitted changes only)

1. **Get the staged diff stat:**

   ```bash
   git diff --cached --stat
   ```

2. **Get the unstaged diff stat:**

   ```bash
   git diff --stat
   ```

3. **Get the full diff** of all uncommitted changes for context:

   ```bash
   git diff HEAD
   ```

---

## Step 2 — Classify Changes (Base vs Pro)

- Review the changes and classify them as either **Base plugin features** or **Pro plugin features / Pro-gated features**.
- Pro-gated features should **only** be added to the Pro plugin's changelog.
- Base features should **only** be added to the Base plugin's changelog.

---

## Step 3 — Read Existing Changelogs

- **Base Plugin:** Read `changelog.txt` (or `readme.txt`) in the base plugin directory to match formatting and version numbering.
- **Pro Plugin:** Check if a pro plugin directory (e.g., `../antimanual-pro` or sibling directory) exists. If it does, read its `changelog.txt` as well.

---

## Step 4 — Determine Target Version

- **Specific version number:** If the user specifies a version (e.g., "3.2.0"), create a new version block using that number.
- **"New version":** If the user asks for a "new version", increment the minor version from the current top entry and create a new version block.
- **No version mentioned:** **Add the changelog entries to the current (top-most) version block** already in the file. Do not create a new version block.

---

## Step 5 — Write Changelog Entries

### Output Format

```
= X.Y.Z (DD Month YYYY) =
New: Feature Name – Description of what the feature does and why it matters
Updated: Area – What was updated, changed, or replaced and how it affects users
Improved: Area – What was improved and how it benefits users
Fixed: Description of the bug that was fixed
```

### Entry Types

| Type        | When to use                                                                                      |
|-------------|--------------------------------------------------------------------------------------------------|
| `New:`      | A brand-new feature, page, setting, or capability that didn't exist before.                      |
| `Updated:`  | An existing feature, dependency, or behavior that was changed, replaced, or brought up to date.  |
| `Improved:` | An existing feature that was enhanced for better performance, UX, or reliability.                 |
| `Fixed:`    | A bug, error, or broken behavior that was corrected.                                             |

> **`Updated` vs `Improved`:** Use `Updated` when something was *changed or replaced* (e.g., swapped a library, changed default values, redesigned a UI section). Use `Improved` when the same thing was made *better without fundamentally changing it* (e.g., faster load time, smoother animation, clearer wording).

### Formatting Rules

- Each entry starts with: `New:`, `Updated:`, `Improved:`, or `Fixed:`
- Use an **em dash** (–) to separate the feature name from its description.
- Feature names should be **title-cased** and descriptive (e.g., "Release Reactions", "Roadmap Add Column").
- Descriptions should explain **what** the feature does and **why** it matters to the user.
- Group entries by type in this order: **New → Updated → Improved → Fixed**.
- For a new version block, use the **current date** for the version header.
- Do **not** use bullet markers (`*`, `-`) — entries are plain lines.

---

## Step 6 — Writing Guidelines

These rules ensure the changelog reads well for end users (site admins, non-developers).

### Tone & Audience

- Write for **site administrators and end users**, not developers.
- Use **plain language** — avoid technical jargon, internal class names, file names, and code references.
- Focus on **outcomes and benefits** ("faster load time", "cleaner layout") rather than implementation details ("refactored CSS modules", "migrated from X to Y").
- Be **confident and positive** — never use alarming language like "critical vulnerability patched" or "major bug fixed". Keep it calm.

### Consolidation

- **Merge related commits into one entry.** If 5 commits all improve the chatbot UI, write one `Improved:` entry that summarizes the overall improvement.
- **Don't list every small tweak.** Minor code refactors, linting fixes, comment changes, and internal restructuring that users never see should be **omitted entirely**.
- Aim for a **concise, scannable list** — typically 5–15 entries per release, not 30+.

### What to Include

| Include                                         | Omit                                              |
|-------------------------------------------------|---------------------------------------------------|
| New user-facing features                        | Internal refactors with no visible effect          |
| Behavior changes users will notice              | Code style / linting changes                      |
| Bug fixes that affected users                   | Test-only changes                                 |
| Performance improvements users can feel          | Developer tooling updates (build config, CI, etc.) |
| Security hardening (stated calmly)               | Dependency bumps with no user impact               |
| Changed defaults, removed features, migrations  | File renaming or directory restructuring           |

### Security Entries

- Use a calm, professional tone: `Security: Strengthened endpoint access controls.`
- Never reveal specifics of the vulnerability or attack vector.
- Never use words like "critical", "exploit", "attack surface" in user-facing changelogs.

### What NOT to Write

```
❌ Fixed: Resolved N+1 query issue in PostingController::list() by eager-loading meta via $wpdb->prepare()
✅ Fixed: Auto Posting – Resolved a performance issue when loading posting items.

❌ Improved: Refactored _components.module.scss and _utils.module.scss into centralized CSS modules
✅ Improved: Admin Architecture – Consolidated admin stylesheets for better performance.

❌ Fixed: Security – Mitigated unrestricted file upload vulnerability in AI controllers
✅ Fixed: Security – Added file type validation for uploads.

❌ Updated: Swapped lodash.cloneDeep for structuredClone() in post-content.ts utility
(omit entirely — no user-visible effect)
```

---

## Step 7 — Quality Checklist

Before writing the entries to the file, validate against this checklist:

- [ ] Every entry is written from the **user's perspective**, not the developer's.
- [ ] No internal file names, class names, function names, or technical jargon in any entry.
- [ ] Related commits are **consolidated** into single, meaningful entries.
- [ ] Purely internal changes (refactors, linting, tests) are **omitted**.
- [ ] Entries are **grouped** in the correct order: New → Updated → Improved → Fixed.
- [ ] Feature names are **title-cased** and use an **em dash** separator.
- [ ] Security entries are **calm and non-specific** about vulnerabilities.
- [ ] The total entry count is **reasonable** (aim for 5–15 per release, not 30+).
- [ ] The changelog **reads naturally** when scanned top to bottom.

---

## Step 8 — Update Changelog Files

- For the **Base Plugin**: Update its `changelog.txt` with base changes only.
- For the **Pro Plugin**: If it exists and there are pro changes, update its `changelog.txt` with pro changes only.
- **Adding to the current version:** Insert new entries into the appropriate type groups (New/Updated/Improved/Fixed) within the existing top-most version block.
- **Creating a new version:** Insert the new version block at the top of the changelog, right after the `== Changelog ==` header line, with a blank line before the previous version entry.
