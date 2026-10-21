# Board Report Playbook: Canva mechanics, layout, and story spec

> Part of the [board-report](../SKILL.md) skill.

Durable reference for `board-report`. The SKILL.md governs sequence and judgment; this
file holds the deterministic detail so the SKILL.md stays lean.

All tools below are on the Canva connector (server prefix omitted). Every tool takes a
`user_intent` string; keep it short and specific.

---

## Contents

1. The verified operation sequence
2. Gotchas (each cost a real debugging moment)
3. Report layout taxonomy
4. Story spec schema
5. Voice grounding

## 1. The verified operation sequence

This sequence was proven end to end on a real report. Follow it in order.

1. `list-folder-items` on `{{CANVA_REPORT_FOLDER}}` → pick the base design ID.
2. `list-folder-items` on `{{CANVA_PHOTO_FOLDER}}`, `item_types: ["image"]` → get image
   asset IDs and filenames.
3. `get-assets` with the image asset IDs → view each photo (the thumbnail returns as a real
   image) and match to stories.
4. `copy-design` the base → new design ID. Work only on this copy.
5. `start-editing-transaction` on the copy → returns `transaction_id`, the `pages` array
   (with `is_responsive` per page), every text element with its `element_id` and current
   text, and every image `fill` with its `element_id` and `asset_id`. Read these fresh;
   do not reuse element IDs from a prior month.
6. `perform-editing-operations`: pass the `transaction_id`, the `pages` array from step 5,
   the `page_index` of the first page you touch, and the operations in bulk:
   - `replace_text`: `element_id` + new `text`.
   - `update_fill`: `element_id` + `asset_type: "image"` + `asset_id` + `alt_text`.
   - `insert_fill`: `page_id` + `asset_id` + `alt_text` (+ optional position and size).
   - `find_and_replace_text`, `format_text`, `delete_element`, `resize_element`,
     `position_element` as needed.
   The response returns a thumbnail image and per-op success status. Show the thumbnail.
7. `commit-editing-transaction` with the `transaction_id`. Changes are DRAFT until this
   call; they are lost if you do not commit.
8. `get-design` on the design ID → fresh post-commit `view_url` and `edit_url`. Surface
   these.
9. `get-export-formats` on the design ID → confirm `pdf` is supported.
10. `export-design`: `format: {type: "pdf", size: "letter", export_quality: "pro"}`.
    Returns a download URL. Surface it.

---

## 2. Gotchas (each cost a real debugging moment)

- **Stale links.** `/d/` short links are snapshots from when they were generated. A link
  captured before commit shows the pre-edit design and reads as "the edit failed." Always
  `get-design` after commit and use those links, or the stable `/design/{id}` URL. The
  design ID never changes.
- **Responsive pages restrict ops.** On a page with `is_responsive: true`, only
  `update_title`, `replace_text`, `update_fill`, `delete_element`, `find_and_replace_text`
  are allowed. Check the flag before building the op list. Board reports are normally
  fixed-page (`false`), which allows everything.
- **Images need a Canva asset ID.** `update_fill` and `insert_fill` take an `asset_id`. The
  only connector path to create one from outside Canva is `upload-asset-from-url`, which
  accepts ONLY already-public HTTPS URLs. Local and cloud-drive files cannot be ingested
  this way, and private files must never be published to manufacture a URL. Practical
  path: the user uploads to a Canva folder.
- **`get-assets` is a lookup, not a browse.** It takes a list of asset IDs and returns
  metadata and a viewable thumbnail. It cannot enumerate a folder. Use `list-folder-items`
  (type `image`) to discover IDs first.
- **Uploads vs folder.** Loose uploads in the "Uploads" area are hard to enumerate; a named
  Canva folder lists cleanly via `list-folder-items`. Ask for a folder.
- **`permission_denied` on some assets.** Assets owned elsewhere in the team (for example
  a chart image placed by another user) can refuse `get-assets`. The user's own uploads
  are theirs, so this does not affect placing their photos; it only blocks reading
  someone else's asset.
- **Thumbnail is low-res.** The `get-assets` preview is a couple hundred pixels, enough to
  judge subject and composition, not fine detail. The asset placed in the report is the
  full-resolution original; only the preview is downsized.
- **Commit gate.** Only commit on the working copy after the user okays the thumbnail.
  Never commit edits onto a real prior month's report.

---

## 3. Report layout taxonomy

The reports fall into two families. Identify the base's family before copying.

**Story-card months (the common case).** Cover page with a hero story (headline and large
photo) and the masthead block: "President's Report" / "[Month] [Year]" / "to the Board of
Trustees". An Upcoming Events block (typically three items: date, name, location). Then
several story cards, each a headline, one to three short paragraphs, and one or more
photos. Topics cluster into: leadership and personnel news, ceremonies and celebrations,
capital and facilities projects, cultural-heritage events, program and workforce
highlights, athletics.

**Data months.** Same masthead, but the body is an enrollment or metrics layout: a lead
narrative plus stat callouts (headcount, full-time equivalent students, total enrollment
with year-over-year changes) and chart images. Fewer photos, more numbers. Used when the
month is an enrollment update rather than an events roundup.

The masthead block (President's Report / month / to the Board of Trustees) is constant
across both.

---

## 4. Story spec schema

The approval artifact. Present it as readable text at the Step 3 gate. One block per story.

```
cover:
  hero_headline: <the month's lead event, short>
  hero_photo: <asset filename or "TBD">
  month: <Month Year>

upcoming_events:
  - { date: <Mon DD>, name: <event>, location: <venue> }
  - { date: <Mon DD>, name: <event>, location: <venue> }
  - { date: <Mon DD>, name: <event>, location: <venue> }

stories:
  - headline: <short, specific>
    section: <leadership | ceremony | facilities | heritage | program | athletics>
    body: |
      <about 100 to 150 words, board voice, third person, zero em dashes>
    photo: <asset filename, or "none">
    confirm: <any name/date/number to verify, or "-">
```

Rules for the body copy:

- Third-person institutional voice. "The College honored...", not "We honored...".
- About 100 to 150 words per story. The lead story can run slightly longer.
- Lead with the news, then the color (who spoke, what happened, why it matters).
- Numbers and names must be verified. Mark anything unconfirmed in `confirm`.
- Zero em dashes. Commas, shorter sentences, or rewrite.

---

## 5. Voice grounding

Board-report prose is the third-person institutional register, closest to the
organization's press releases. Ground it in the local samples (read through the session's
folder access, never a hardcoded path):

- Press releases in `{{VOICE_SAMPLES_FOLDER}}`: the closest register match.
- Media advisories and email announcements in the same folder: event framing and the
  organization's event vocabulary.
- Prior board reports in Canva: read two or three recent ones with `get-design-content`
  to absorb the house cadence and section patterns.

This is NOT the first-person podium voice of executive remarks. If a line sounds like a
person speaking, rewrite it as institutional narration.
