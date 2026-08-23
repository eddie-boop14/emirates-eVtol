# UAE eVTOL — the primary-source reference for eVTOL in the United Arab Emirates

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.22068156.svg)](https://doi.org/10.5281/zenodo.22068156)
[![License: CC BY 4.0](https://img.shields.io/badge/License-CC_BY_4.0-lightgrey.svg)](https://creativecommons.org/licenses/by/4.0/)
[![Site](https://img.shields.io/badge/site-evtolemirates.com-0ea5c4.svg)](https://evtolemirates.com)

Every vertiport, route, aircraft, operator and regulator in the UAE eVTOL
network — **49 entities**, each with a status enum, coordinates, a
per-record *last-verified* date and a full list of named primary sources
(operator IR releases, regulator statements, government announcements).
No aggregation, no invented launch dates: the editorial bar is
**contracted, built, flight-tested, or regulated**, and entities that fail
it are documented transparently on the
[did-not-make-the-cut](https://evtolemirates.com/explainers/did-not-make-the-cut.html) page.

Published as a static site in five languages (EN · AR · FR · DE · ZH) and as
open data. Sister project: [Qatar eVTOL](https://github.com/eddie-boop14/Quatar-eVtol) · [qatarevtol.com](https://qatarevtol.com).

## The data

| Surface | URL |
|---|---|
| Dataset documentation | https://evtolemirates.com/data.html |
| Full corpus (JSON, with i18n + sources) | https://evtolemirates.com/entities.json |
| Flat export (CSV) | https://evtolemirates.com/entities.csv |
| Per-entity JSON API | https://evtolemirates.com/api/index.json → `/api/{type}/{slug}.json` |
| **MCP server** (for AI agents) | `POST https://evtolemirates.com/mcp` — tools: `get_entity`, `search_entities`, `list_changes` |
| Fact-change log | [changes.html](https://evtolemirates.com/changes.html) · [changes.xml](https://evtolemirates.com/changes.xml) (Atom) · [changes.json](https://evtolemirates.com/changes.json) |
| Re-verification feed | https://evtolemirates.com/feed.xml (Atom) |
| For LLMs | [llms.txt](https://evtolemirates.com/llms.txt) · [llms-full.txt](https://evtolemirates.com/llms-full.txt) |
| OpenAPI description | https://evtolemirates.com/.well-known/openapi.json |

Quick taste — one entity, one request:

```bash
curl -s https://evtolemirates.com/api/vertiport/dxb-vertiport.json
```

Or ask the MCP server:

```bash
curl -s -X POST https://evtolemirates.com/mcp -H 'content-type: application/json' \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/call","params":{"name":"get_entity","arguments":{"slug":"dxb-vertiport"}}}'
```

## How correctness is enforced

The repository is the dataset; the HTML is generated/hand-maintained on top
of it, and a build gate keeps the two from drifting:

- **`entities.json`** — source of truth: status enums, coordinates, dates,
  per-source citations, per-page fact atoms (`facts`) with a five-locale
  label vocabulary (`data/facts_vocab.json`).
- **`guard.sh`** — 24 invariants, run on every deploy. Among them: every
  page canonicalised and in the sitemap, every sitemap URL existing, fonts
  self-hosted, and *atoms check* — every fact row on every page in every
  locale must equal the stored atoms, and every rendered status must equal
  the dataset's enum. Drift is build-breaking, not silent.
- **`FRESHNESS.md`** — the re-verification protocol: primary sources are
  re-read on a risk-ranked schedule and `last_verified` moves only when a
  human (or supervised sweep) actually re-read them. Full-corpus sweep last
  completed **2026-08-22**.
- **Build tooling** — `build_data.py` (dataset artefacts), `build_og.py`
  (social cards from the data), `build_api.py` (JSON endpoints),
  `build_changes.py` (change log recovered from git history),
  `build_atoms.py` (fact atoms: harvest / stamp / check),
  `build_redirects.py`, `build_sister.py`, `freshness.py`.

## Citing

Attribution is the only condition of the CC BY 4.0 licence — it is also the
point. Cite the dataset as
[`CITATION.cff`](CITATION.cff) describes, or simply:

> UAE eVTOL Reference Dataset, evtolemirates.com.
> https://doi.org/10.5281/zenodo.22068156

The DOI always resolves to the latest versioned release.

## Independence

This is an independent reference. It is not affiliated with any operator,
regulator, or government body documented on the site. Source links go to
original documents — verify anything that matters before relying on it.
