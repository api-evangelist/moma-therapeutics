---
name: moma-therapeutics-track-news-archive
description: >-
  Poll MOMA Therapeutics' news archive for new press releases, media coverage, peer-reviewed
  publications and conference presentations, and resolve each item to the artifact it actually
  points at — which is frequently a PDF or an off-site article rather than the post body.
api: moma-therapeutics:moma-therapeutics-posts-api
operations:
- listPosts
- getPost
- listCategories
- getMediaItem
---

# Track the MOMA Therapeutics news archive

MOMA Therapeutics publishes 32 news items covering April 2020 through August 2026 — funding
rounds, the Roche and Bayer collaborations, leadership appointments, and the AACR and clinical
data readouts for MOMA-313 and MOMA-341. The archive is readable anonymously at
`https://momatx.com/wp-json/wp/v2/posts`.

## Steps

1. **Read the newest slice first.** `GET /wp/v2/posts?per_page=20&orderby=date&order=desc`.
   Read `X-WP-Total` from the response headers to size the archive before paging — it was 32 on
   2026-08-26. `X-WP-TotalPages` tells you how far to page at your chosen `per_page`, which is
   capped at 100.

2. **Ask only for the fields you need.** `&_fields=id,date,modified,slug,title,link,categories,acf`
   cuts the payload substantially, because `content.rendered` and `yoast_head` dominate the
   default response. There is no expansion parameter, so `_fields` is the only projection lever.

3. **Do incremental pulls with the date bounds, not by diffing.**
   `GET /wp/v2/posts?modified_after=2026-08-01T00:00:00&orderby=modified&order=desc` returns only
   what changed. Track the highest `modified` you have seen and use it as the next lower bound.
   Both `after`/`before` (publication) and `modified_after`/`modified_before` are declared in the
   route index and accept ISO 8601 date-times.

4. **Resolve the item to its real artifact.** This is the step that matters, and skipping it is
   the common mistake. Each post carries an `acf` block:
   - `acf.post_source` — the outlet an item was covered in.
   - `acf.post_external_link` — a URL to coverage hosted off momatx.com.
   - `acf.post_link_to_pdf` — a URL to a poster or paper, usually in the media library.

   Much of the archive is pointers to material MOMA did not host. A consumer that reads only
   `content.rendered` gets a stub and concludes the archive is thin. Any of these three fields
   may come back as boolean `false` rather than a string when unset — check the type before
   using it as a URL.

5. **Segment by category if you want announcements only.** `GET /wp/v2/categories` returns the
   four registered terms (Blog and Press Release among them). Filter with
   `GET /wp/v2/posts?categories=<id>`. Do not filter on `tags` — the `post_tag` taxonomy is
   registered but empty, so it will silently return nothing.

6. **Fetch a poster or paper.** When `acf.post_link_to_pdf` points into the media library, the
   file is also reachable as an object: `GET /wp/v2/media?search=<filename>` then read
   `source_url`, `mime_type` and `filesize` from the match.

## Rules

- **Every call is a GET.** There is no write surface reachable without credentials, so nothing in
  this skill can be undone because nothing can be done. See
  `conventions/moma-therapeutics-conventions.yml`.
- **Branch on `code`, never on `message`.** Errors come back as
  `{"code": "...", "message": "...", "data": {"status": ...}}` — not RFC 9457. A 404 with
  `rest_post_invalid_id` means the ID does not exist; `rest_no_route` means you built a bad path.
  Full catalogue in `errors/moma-therapeutics-problem-types.yml`.
- **Stay inside the declared bounds.** `per_page` above 100 returns 400 `rest_invalid_param` with
  a nested `rest_out_of_bounds`; an unlisted `orderby` returns the same with `rest_not_in_enum`.
- **Poll no faster than the cache.** Responses carry `cache-control: max-age=600` and there is no
  ETag or Last-Modified, so a conditional request is impossible and every poll re-transfers the
  full body. Ten minutes is the useful floor. There is no rate-limit header to tell you when you
  are close to a limit, and no documented ceiling — see
  `rate-limits/moma-therapeutics-rate-limits.yml`.
- **Do not attempt authentication.** MOMA Therapeutics issues no credentials and operates no
  developer program. A 401 here is final, not a prompt to sign up.
