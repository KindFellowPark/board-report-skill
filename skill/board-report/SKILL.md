---
name: board-report
description: >
  Build the monthly "President's Report to the Board of Trustees" in Canva by copying
  last month's report, drafting each story in the board's institutional voice, and placing
  the month's event photos into the frames, then exporting a board-ready PDF. Triggers on:
  "build the board report", "make the [month] board report", "President's Report to the
  Board", "draft this month's board report", "start the board report", or the user handing
  over a month's story list plus photos for the board packet. Canva-connector driven end to
  end. Does NOT design a report from scratch (it always copies an existing report to
  preserve layout), does NOT invent facts, and does NOT post or share the report.
---

# Board Report Builder

The monthly President's Report is a photo-forward story-card document that lives in
Canva. This skill produces it the way it is actually made: copy the most recent report
so the exact layout is preserved, draft each story in the board's third-person
institutional voice, place the month's photos into the frames, and export a PDF for the
packet. The Canva design is the editable source of truth; the PDF is a derived export.

This is a **hybrid workflow**. The Canva mechanics are deterministic and live in
`references/playbook.md`. The two judgment calls, writing each story in board voice and
matching photos to stories, need the user's material and the user's approval. The human
gate sits at the boundary: the story spec is approved as text before anything touches
Canva.

## Reading map

| When | Read |
|---|---|
| When a step says see Decision: which base, photo access, matching photos to stories, or responsive pages | [references/decisions.md](references/decisions.md) |

## Setup: file pointers (fill in once per install)

This skill points at a few locations that differ on every computer. Replace each
placeholder below with the real location before the first run. Claude can do this for you:
give it the skill folder and tell it where each item lives.

| Placeholder | What it points at |
|---|---|
| `{{CANVA_REPORT_FOLDER}}` | The Canva folder that holds past board reports |
| `{{CANVA_PHOTO_FOLDER}}` | The Canva folder the month's photos are uploaded to |
| `{{VOICE_SAMPLES_FOLDER}}` | A folder of the organization's press releases, media advisories and announcements |
| `{{PEOPLE_LIST}}` | A file listing names and titles of leadership and frequent partners |
| `{{OUTPUT_FOLDER}}` | Where a filed copy of the exported PDF is saved |

## Read before you build (non-negotiables)

1. **Board voice, not the drafter's.** The report speaks in the organization's third-person
   institutional voice, the register of a press release or the President's report, never
   first-person podium remarks. Ground the voice in `{{VOICE_SAMPLES_FOLDER}}` (press
   releases, media advisories, email announcements) and in prior board reports read from
   Canva. Each story runs about 100 to 150 words.
2. **Zero em dashes.** The report is board-facing prose. Use commas, shorter sentences,
   or a rewrite.
3. **Never invent facts.** Every name, title, date, number, program, and event is
   verified before it enters the report. A wrong athletic director's name or a misspelled
   program in the President's report to the Board is the failure this skill exists to
   prevent. Resolve names and titles against `{{PEOPLE_LIST}}`; do not guess.
4. **Always copy, never rebuild.** The layout is preserved by copying an existing report,
   not regenerating one. `generate-design-structured` is the wrong tool; it cannot
   reproduce the specific layout.
5. **Work on a copy, protect the originals.** Never open an editing transaction on a real
   prior report. Copy first, edit the copy.
6. **Refresh links after every commit.** Canva `/d/` short links are snapshots taken when
   generated; a link captured before a commit keeps showing the pre-edit state. After any
   commit, call `get-design` and surface only the fresh links (or the stable
   `/design/{id}` URL). Never hand the user a link captured earlier in the run.

## On trigger: intake

Gather, but keep it tight. The load-bearing inputs are:

- **Month** of the report (sets the masthead and the export filename).
- **The story list**: which events and items go in this month, roughly in priority order.
  The user provides this; the newest events lead.
- **Source material** for each story: event name, who spoke, key numbers, notable
  moments, any names that must be spelled right.
- **Photos**: confirm they are uploaded to `{{CANVA_PHOTO_FOLDER}}` (see Decision: photo
  access).

Do not over-collect. Month, the story list, and the photo folder are the minimum.

## Step 1: Locate the base report and the photos

Read `{{CANVA_REPORT_FOLDER}}` with `list-folder-items` and pick the base to copy (see
Decision: which base). Confirm the photo folder exists and list its images with
`list-folder-items` filtered to `image`. If either is missing, stop and ask.

Read the voice samples and the people list through whatever folder access the session
provides. Never hardcode a path that only exists on one computer.

## Step 2: Draft the story spec

Turn the story list and source material into a **story spec**: one block per story with a
headline, a body of about 120 words in board voice, its section, and which photo it should
use. Include the cover hero, the month, and the Upcoming Events items. The schema is in
`references/playbook.md`.

View each photo with `get-assets` (the thumbnail comes back as an image you can actually
see) and match it to a story by content. Note any photo whose people or event you cannot
identify from the image alone.

## Step 3: GATE: approve the story spec

Present the full story spec to the user as text and get approval before touching Canva.
Flag every fact that needs confirmation and every photo whose identity you could not read
from the pixels. Cheap to fix here, expensive to fix in Canva.

Do not proceed to Canva edits until the user approves.

## Step 4: Copy the base report

`copy-design` the chosen base. Retitle the copy to the working month. All editing happens
on this copy.

## Step 5: Populate text and photos

`start-editing-transaction` on the copy to read the live element structure (never assume
element IDs carry over between months). Then `perform-editing-operations` in bulk:

- `replace_text` for each headline and body, using the approved spec.
- `update_fill` to swap each photo into its frame by asset ID. **Write alt text in the
  same operation.** Accessibility is built in, not added later (ADA Title II).
- `insert_fill` only when a story needs a photo the layout has no frame for.

Show the user the returned thumbnail after the operations. See Decision: responsive pages
before editing.

## Step 6: Commit, refresh, export

Get the user's OK, then `commit-editing-transaction`. Immediately `get-design` for fresh
post-commit links and surface those. Confirm the export format with `get-export-formats`,
then `export-design` as PDF (letter, pro). Save a filed copy of the PDF to
`{{OUTPUT_FOLDER}}` as `board_report_[month]_YYYY-MM-DD.pdf` when the session can retrieve
it; the Canva design remains the source.

## Output

- The populated Canva design (the editable source), retitled to the month.
- Fresh post-commit view and edit links, pulled via `get-design`.
- A board-ready PDF (letter, pro), link surfaced; a filed copy saved to `{{OUTPUT_FOLDER}}`
  as `board_report_[month]_YYYY-MM-DD.pdf` when retrievable.

## What this skill does NOT do

- **Does not design from scratch.** It always copies an existing report.
- **Does not post, share, or send** the report. It builds and exports; distribution is the
  user's.
- **Does not ingest local or cloud-drive photos.** Photos reach Canva by upload to a Canva
  folder or a public URL; the skill never publishes private files to make a URL.
- **Does not draft podium remarks.** First-person executive speech is a different job.
