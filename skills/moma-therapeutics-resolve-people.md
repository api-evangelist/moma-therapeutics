---
name: moma-therapeutics-resolve-people
description: >-
  Enumerate and classify the people at MOMA Therapeutics — leadership, board of directors,
  scientific advisory board and founders — from the company's `team` custom post type, including
  each person's role and headshot.
api: moma-therapeutics:moma-therapeutics-team-api
operations:
- listTeamMembers
- getTeamMember
- listTeamTypes
- getTeamType
- getMediaItem
---

# Resolve the MOMA Therapeutics team

MOMA Therapeutics models its people as a first-class custom post type rather than as WordPress
users, which is why `/wp/v2/users` answers 401 while the people themselves are fully readable.
There were 59 published records on 2026-08-26.

## Steps

1. **Read the classification vocabulary first.** `GET /wp/v2/team_types` returns the three
   registered terms — Founders among them — each with an `id`, a `slug` and a `count` of how many
   people carry it. Fetching this before the people lets you bucket in one pass instead of
   post-processing free text.

2. **List the people.** `GET /wp/v2/team?per_page=100&_fields=id,slug,title,team_types,acf,featured_media,link`.
   One page covers the whole set at `per_page=100`; confirm against `X-WP-Total`.

3. **Read the role from ACF, not from the title.** `title.rendered` is the person's name.
   `acf.member_title` is their role at MOMA — Chief Executive Officer, Chief Scientific Officer,
   Chair of the Board and so on. `acf.member_video` is an optional video URL. Both may return
   boolean `false` rather than a string when unset.

4. **Bucket by `team_types`.** Each record's `team_types` is an array of term IDs from step 1.
   This is the only reliable separator between the board, the scientific advisory board, the
   executive team and the founders — the biography prose is not structured.

5. **Get the biography.** `GET /wp/v2/team/{id}` returns the full `content.rendered`. Omit
   `_fields` for this call, or ask for `content` explicitly.

6. **Resolve the headshot.** `featured_media` is a media library ID; `GET /wp/v2/media/{id}` gives
   `source_url`, `alt_text` and the generated size variants under `media_details.sizes`. Adding
   `?_embed` to the team request inlines the same object under `_embedded['wp:featuredmedia']`
   and saves you one call per person — 59 calls saved across the set.

## Rules

- **Do not try `/wp/v2/users`.** It returns 401 `rest_user_cannot_view` and always will. The
  `author` field on other post types is likewise unresolvable anonymously. People live in
  `/wp/v2/team`, not in the user table.
- **IDs are shared across post types.** A valid `team` ID is not a valid `posts` ID. Always fetch
  an ID back through the collection it came from, or check the `type` field. See
  `data-model/moma-therapeutics-data-model.yml`.
- **This is personal data about named individuals.** Read it for the purpose you fetched it for
  — company research, contact routing, org-chart resolution — and do not compile it into a
  contact list or feed it to enrichment or outreach tooling. It is published as a corporate About
  page, not as a directory.
- **Read-only.** Every operation here is a GET; there is no write path and therefore nothing to
  reverse.
