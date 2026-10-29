---
kind: skill-reference
owns: [board-report decisions: which base report, photo access, matching photos to stories, responsive pages]
read_when: When a step says see Decision: which base, photo access, matching photos to stories, or responsive pages
status: live
---

> Part of the [board-report](../SKILL.md) skill, read from § Reading map

# Board Report: decisions

Moved out of SKILL.md word for word.

## Decision: which base report to copy

**Options:**
- Most recent report: default. Preserves the current layout and any recent design
  refinements.
- An earlier report of a specific layout family, when this month's content matches a
  different structure. The reports fall into two families: **story-card months** (cover
  hero plus several headline, narrative and photo cards) and **data months** (enrollment
  or metric layouts with stat callouts and charts). Match the base to the month's content.

**Default:** most recent report of the matching layout family.

**What would make you abandon this step:** if no existing report matches the month's
structure, stop and ask the user rather than generating a layout from scratch.

## Decision: photo access

**Options:**
- Photos in a Canva folder: the working path. The user drops the month's photos into
  `{{CANVA_PHOTO_FOLDER}}`; list them with `list-folder-items` (type `image`) and place by
  asset ID.
- Photo already at a public HTTPS URL: `upload-asset-from-url` brings it in with no manual
  step. Applies only to already-public images (the organization's website, a public press
  gallery).

**Default:** Canva folder. `get-assets` is a lookup by ID, not a browse, so photos must
live in a folder (not the loose Uploads area) to be discoverable.

**What would make you abandon this step:** local or cloud-drive files cannot be pulled in
through the connector, and private files must never be published to manufacture a URL. If
photos are not in Canva, stop and ask the user to drop them in the folder.

## Decision: matching photos to stories

**Options:**
- Match by sight: default. The `get-assets` thumbnail is viewable; identify the event
  from the image and place accordingly.
- Match by filename: tiebreaker when photos are visually similar (several ceremonies that
  all look alike). A light convention like `01_commencement.jpg` anchors the match.

**Default:** match by sight, use filename as tiebreaker.

**When to ask the user:** when the story needs a specific person or event identity the
pixels cannot confirm (a name, which of two near-identical events). Ask for a one-line
caption rather than guessing.

## Decision: responsive pages

Before `perform-editing-operations`, check each page's `is_responsive` flag from the
transaction. On responsive pages only these ops are allowed: `update_title`, `replace_text`,
`update_fill`, `delete_element`, `find_and_replace_text`. Position, resize, and insert are
not.

**Default:** board reports are fixed-page (`is_responsive: false`), which allows the full
op set. If a page is responsive and the plan needs an unsupported op, tell the user and
adjust the plan rather than failing mid-transaction.
