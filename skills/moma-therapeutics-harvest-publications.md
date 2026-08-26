---
name: moma-therapeutics-harvest-publications
description: >-
  Find MOMA Therapeutics' scientific output — AACR posters, preprints and peer-reviewed papers on
  the Polθ helicase inhibitor MOMA-313 and the WRN helicase inhibitor MOMA-341 — and retrieve the
  underlying PDFs from the media library.
api: moma-therapeutics:moma-therapeutics-media-api
operations:
- search
- listPosts
- listMedia
- getMediaItem
---

# Harvest MOMA Therapeutics publications and posters

The scientific record is spread across two collections that do not cross-reference each other
cleanly: the news archive announces a poster or paper, and the media library holds the PDF. This
skill joins them.

## Steps

1. **Search across everything first.** `GET /wp/v2/search?search=<term>&per_page=100` returns
   lightweight hits — `id`, `title`, `url`, `type`, `subtype` — across posts, pages and team
   records. An unfiltered query returned 42 results on 2026-08-26. Useful terms: `MOMA-313`,
   `MOMA-341`, `WRN`, `helicase`, `polymerase theta`, `PARP1`, `AACR`.

2. **Resolve each hit to its full object.** Search returns a pointer, not an entity. Read
   `subtype` and fetch from the owning collection — `subtype: post` to `/wp/v2/posts/{id}`,
   `page` to `/wp/v2/pages/{id}`, `team` to `/wp/v2/team/{id}`.

3. **Pull the PDF link off the post.** On a `post`, `acf.post_link_to_pdf` is the direct link to
   the poster or paper and `acf.post_source` names the journal or conference. These are the two
   fields that make the archive useful for literature work; neither appears in
   `content.rendered`.

4. **Or go at the media library directly.** `GET /wp/v2/media?per_page=100&_fields=id,slug,title,source_url,mime_type,filesize,date`
   returns the 211 attachments. Filenames are descriptive — the AACR 2026 poster for MOMA-313 is
   stored as `MOMA-313-Poster-AACR-2026-...`. Filter to documents by checking `mime_type` for
   `application/pdf`, or narrow with `&search=poster` / `&search=AACR`.

5. **Page the library properly.** 211 items at the `per_page` ceiling of 100 is three pages. Read
   `X-WP-TotalPages` rather than guessing, and use `page=1..n`.

6. **Fetch the file.** `source_url` is a direct, unauthenticated URL to the stored file. Check
   `filesize` before downloading.

## Rules

- **`mime_type` is the reliable document filter,** not the file extension in the slug.
- **`acf.post_link_to_pdf` may point off-site.** It is a URL, not a media ID, and is not
  validated against the library. Do not assume it resolves to a `source_url` you already have.
- **Respect the cache.** `cache-control: max-age=600`, no ETag, no Last-Modified. Cache the media
  listing locally rather than re-paging it; the library changes on the order of weeks.
- **Everything here is a GET.** Nothing in this skill mutates anything, so there is nothing to
  undo. See `conventions/moma-therapeutics-conventions.yml`.
- **These are the company's own published materials.** Attribute them to MOMA Therapeutics and to
  the journal or conference named in `acf.post_source`; do not republish PDFs as your own.
