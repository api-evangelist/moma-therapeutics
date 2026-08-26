# MOMA Therapeutics

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **Not from the company, and here with a question?** You are welcome here — we would rather be the
> front line and point you the right way than have a good report go nowhere. What this repository
> can answer is narrow, though, so it is worth knowing who you are actually looking for:
>
> - **A question about how the API works, an account, billing, or a bug in the service** — that is
>   the company's own support, not us. We profile this API; we do not operate it and cannot see
>   your account.
> - **A bug in an open-source project we only catalog** — file it on that project's own repository.
>   This has happened with a real and correct bug report that reached us instead of the people who
>   could fix it, which helped nobody.
> - **Anything about this listing itself** — the description, the tags, the rating, a missing or
>   wrong artifact — is ours. Open an issue here.
> - **Not sure, or something general about API Evangelist or APIs.io** — open an issue on the
>   [APIs.io Inbox](https://github.com/api-search/inbox) and we will route it.
>
> **This repository contains no software, and we will never ask you to download anything.** There is
> no build, release, installer, or binary here — only text and machine-readable API descriptions, so
> there is nothing here that can be "corrupt" or need "repairing". Any issue, comment, or email
> claiming otherwise and offering a download link is not from us and is hostile. Do not follow the
> link; it is a lure. Report it to GitHub and, if you like, tell us at
> [info@apievangelist.com](mailto:info@apievangelist.com) so we can take it down.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

MOMA Therapeutics is a Cambridge, Massachusetts drug-discovery company founded in 2020 that builds
precision medicines against **molecular machines** — the highly dynamic proteins that convert chemical
energy stored in cells into regulatory work such as protein transport, DNA repair and degradation, and
whose constant conformational shape-shifting has historically made them undruggable. Its proprietary
**KNOMATIC** platform pairs structural and functional-genomics target validation with high-throughput
fragment and DNA-encoded-library screening, CRISPR gene editing, AI for detecting dynamic conformational
patterns, and machine-learning lead optimisation.

The company launched in April 2020 with an $86M Series A led by Third Rock Ventures, added a $150M
Series B in May 2022, and has two wholly owned oncology assets in the clinic — **MOMA-313**, a selective
polymerase theta (Polθ) helicase inhibitor that entered Phase 1 in August 2024, and **MOMA-341**, a WRN
helicase inhibitor that dosed its first Phase 1 patient in July 2025. It runs a five-year discovery
collaboration with Roche (January 2024) and a collaboration and licence agreement with Bayer (October 2024).

- Website: https://momatx.com/
- Science: https://momatx.com/science/ · Pipeline: https://momatx.com/pipeline/ · Team: https://momatx.com/team/
- News & Publications: https://momatx.com/news-feed/

## API surface

**MOMA Therapeutics runs no developer program.** There is no product API, no developer portal, no API
reference, no SDK in any registry, no CLI, no sandbox, no pricing, no webhooks, no GraphQL, no gRPC or
WSDL, no MCP server, no A2A agent card and no `/.well-known/` surface of any kind. Every one of those was
probed on 2026-08-26 and the absences are recorded in the artifacts rather than merely assumed.

What *is* published, and what this profile catalogues, is the **read-only WordPress REST content API**
behind `momatx.com` — a real, anonymously reachable, machine-readable surface the company serves at
`https://momatx.com/wp-json/`. Its 219-route index across 12 namespaces is the only machine-readable
contract MOMA Therapeutics publishes, and every OpenAPI document in `openapi/` is an API Evangelist
derivation of it, verified against live anonymous responses.

Two things distinguish it from a stock WordPress install:

- a first-class **`team` custom post type** carrying 59 people — leadership, board, scientific advisory
  board and founders — classified by a MOMA-specific `team_types` taxonomy, and
- an **Advanced Custom Fields** layer carrying the fields the company actually curates: a news item's
  source, external link and PDF link; a page's builder modules; a person's title.

| Collection | Count at harvest |
|---|---|
| News posts (`/wp/v2/posts`) | 32 |
| Team records (`/wp/v2/team`) | 59 |
| Media attachments (`/wp/v2/media`) | 211 |
| Corporate pages (`/wp/v2/pages`) | 9 |
| Search results, unfiltered (`/wp/v2/search`) | 42 |
| Categories / team_types / tags / comments | 4 / 3 / 0 / 0 |

Administrative routes — `/wp/v2/users`, `/wp/v2/settings`, `/wp/v2/menu-items`, `/wp/v2/plugins`,
`/wp/v2/themes` and the whole `wp-abilities/v1` namespace — answer HTTP 401 anonymously and are
deliberately excluded, as is every write operation. Nothing in this repository was obtained with
credentials.

Start with [`llms/moma-therapeutics-llms.txt`](llms/moma-therapeutics-llms.txt) for the agent-facing
summary, or [`skills/`](skills/) for three packaged workflows over the surface.
