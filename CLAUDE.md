# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repository is

Course materials for **数据结构与算法B (Data Structures & Algorithms B / CS201)**, Peking
University, Spring 2026, taught by Hongfei Yan (闫宏飞). It is a **content repository**, not a
software application: the bulk of it is Markdown lecture notes, exam/homework specs, and problem
solutions, plus a handful of standalone Python games. There is no build system, package manifest,
test suite, or CI. Most work here is **authoring and proofreading Markdown**, and occasionally
editing the Python game scripts.

Content is bilingual (Chinese + English); notes and prose are predominantly Simplified Chinese,
while code and algorithm names are English. Preserve the existing language of any file you edit.

## Layout and naming conventions

Root-level lecture notes follow `YYYYMM_DSA_W<week>_<topic>.md` (e.g.
`202603_DSA_W02_BIT_Fenwick.md`, `202604_DSA_W06-08_Tree.md`). The `YYYYMM` / `W<week>` prefix
encodes the teaching timeline — keep it consistent when adding or renaming notes. Exam and
event-specific files use a `YYYYMMDD_` date prefix instead.

- `homework/` — one file per assignment (`assignment1.md` … `assignmentP.md`); problem specs and grading rubrics.
- `cheatsheet/` — student-contributed final-exam cheat sheets (`.md`, `.pdf`, `.docx`), named `<TOPIC>_<STUDENTNAME>`.
- `20260609_ExamPreptoFrontierAI/` — slides/notes from a talk (mixed `.md`, `.pdf`, `.pptx`, `.png`, `.html`).
- `game_*.py` — standalone pygame programs (`game_Sudoku.py`, `game_Minesweeper.py`, `game_PokerMachine.py`); each is self-contained.
- `README.md` — course syllabus, schedule, grading rules, and links (the authoritative course overview).

## Markdown note conventions

Every note opens with a metadata header block that must be kept intact and current:

```markdown
# <Title>

*Updated YYYY-MM-DD HH:MM GMT+8*
 *Compiled by Hongfei Yan (2026 Spring)*
```

When you make a substantive edit to a note, bump the `*Updated ...*` timestamp (timezone is GMT+8 /
Beijing). Homework files carry an additional grading-rubric table near the top — leave its structure
unchanged. Images are referenced by absolute `raw.githubusercontent.com/GMyhf/img/...` URLs, not
local paths; do not rewrite these to relative links.

## Running the games

The `game_*.py` files require pygame:

```bash
pip install pygame
python game_Sudoku.py    # or game_Minesweeper.py, game_PokerMachine.py
```

They open a GUI window and cannot be exercised in a headless terminal — verify changes by reading
the code and, if a display is available, by running them interactively.

## Git

The default branch is `main`. Commit messages are short and describe the content action (e.g.
"Proofread week 1 OOP notes"). The GitHub remote is `GMyhf/2026spring-cs201`.
