# Master-Index.md
This is the **table of contents and operating manual** for the Malzadoe Luau Tutor system.
It defines the **authoritative file list**, **load order**, **roles**, and **update workflow**.
Unless Master Taru overrides, **this index and `Malzadoe-Spec.md` are the source of truth**.

> Repo layout: **all files live in the repo root** (no subfolders) for maximum compatibility.

---

## 0) Session Bootstrap (client → server)
- **Github.md**  
  - Purpose: stores the canonical repo URL used at session start.  
  - Content format (example):  
    ```
    REPO: https://github.com/Taneelkins/my-bot-files
    ```
  - Client behavior: at the beginning of each session, Malzadoe reads `Github.md`, loads the repo, then follows the **Load Order** below.

- **Startup.md** (optional)  
  - Purpose: one‑line bootstrap directive (mirrors the rule above).  
  - Suggested content:  
    ```
    On session start, read Github.md, then load Malzadoe-Spec.md and this Master-Index.md from the repo. Apply all rules and use remaining files as persistent memory/logs.
    ```

---

## 1) Core Directives (read first)
- **Malzadoe-Spec.md**  
  - The **unified rulebook**: persona, honorifics, communication style, teaching flow, code policy, troubleshooting protocol, memory rules, safety, defaults.  
  - On conflict with any other file, **defer to this file** unless Master Taru explicitly overrides.

- **Master-Index.md** _(this file)_  
  - You are here. Defines what exists, where, and how to use it.  
  - Must be updated whenever files are added/removed/renamed.

- **Teaching-Protocol.md**  
  - Concrete teaching mechanics that extend `Malzadoe-Spec.md`:  
    - Lesson pattern (explain → example → check → continue)  
    - Socratic flow, quiz formats, frustration fallback  
    - Answer templates (teach concept, troubleshoot, review code)  
  - If Teaching-Protocol conflicts with `Malzadoe-Spec.md`, **follow `Malzadoe-Spec.md`**.

- **Preferences.md**  
  - Master Taru’s communication and style preferences (tone, brevity, honorific cadence).  
  - Always read and apply; if conflict with `Malzadoe-Spec.md`, defer to `Malzadoe-Spec.md`.

---

## 2) Memory System (read/update continuously)
- **Memory.md**  
  - **Active working memory** for the current arc of sessions.  
  - Keep concise and current; migrate stale entries to `Memory-Archive.md`.

- **Memory-Archive.md**  
  - Long‑term storage. Move closed threads/notes here instead of deleting history.

- **Variable-Mapping.md**  
  - Project‑specific name mappings (e.g., your Roblox services, folder names, constants).  
  - Malzadoe uses this to align examples with your project vocabulary.

---

## 3) Logging & Governance
- **FixLog.csv**  
  - Troubleshooting ledger.  
  - Format: `YYYY-MM-DD, Action, Result, Next Step`.  
  - **Rule:** consult before proposing fixes; never repeat failed steps.

- **Corrections.md**  
  - Correction policy + record of grammar/code corrections already provided.  
  - **Rule:** for corrections, output **only** the corrected text/code (no commentary).

---

## 4) Curriculum & Skills Tracking
- **Curriculum-Tracker.md**  
  - Lessons completed, current focus, next steps, dates.

- **Modules-and-Skills.md**  
  - Skills introduced, modules covered, pending skills to teach.

---

## 5) Update & Automation (server‑side maintenance)
> These files/scripts are executed by GitHub Actions to keep the repo as the single source of truth.

- **.github/workflows/apply-updates.yml**  
  - Workflow that accepts a JSON payload and applies edits to files, then commits.  
  - Input: `updates_json` (schema described below).

- **scripts/apply_updates.py**  
  - Script that performs file operations described in the JSON payload.  
  - Supported ops:  
    - `replace`: overwrite a file with provided content.  
    - `append`: append to file; optional `section` to append under a heading.  
    - `csv-append`: append a row to `FixLog.csv` (or any csv).

> Optional: add a second workflow that **builds a ZIP artifact** of the repo after each update for quick download/backup.

---

## 6) Load Order (session start)
1. `Github.md` → get repo URL (client bootstrap).
2. `Malzadoe-Spec.md` → load and apply complete behavior/persona rules.
3. `Master-Index.md` → verify file list and roles (this file).
4. `Preferences.md` → apply speaking/interaction preferences.
5. `Teaching-Protocol.md` → apply teaching mechanics/templates.
6. Memory set:
   - `Variable-Mapping.md` → map project names.  
   - `Memory.md` → active context.  
   - `Memory-Archive.md` → reference if needed (no bulk load).
7. Governance:
   - `FixLog.csv` → check before proposing troubleshooting steps.  
   - `Corrections.md` → enforce correction output rule.

---

## 7) Conflict Resolution
- If any rule conflicts: **Malzadoe-Spec.md > Teaching-Protocol.md > Preferences.md > others**.  
- Master Taru may override any rule at any time; the latest explicit instruction wins.

---

## 8) JSON Update Schema (for Actions)
When applying end‑of‑session updates, the workflow expects a JSON payload with this shape:

```json
{
  "version": 1,
  "timestamp": "YYYY-MM-DDThh:mm:ssZ",
  "ops": [
    {"file": "Preferences.md", "op": "replace", "content": "<full markdown>"},
    {"file": "Memory.md", "op": "append", "section": "Active Session Notes", "content": "2025-08-19 — Verified Actions save loop."},
    {"file": "FixLog.csv", "op": "csv-append", "row": ["2025-08-19","Pipeline test","Verified write path","Proceed to unify spec"]}
  ],
  "sig": "<optional HMAC hex for verification>"
}
