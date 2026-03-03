# ads-bib-tools

[![DOI](https://zenodo.org/badge/1171942580.svg)](https://doi.org/10.5281/zenodo.18853606)

Tools for managing [NASA ADS](https://ui.adsabs.harvard.edu/) bibliographies — ORCID-based fetching, cleanup, and metadata enrichment for [Quarto](https://quarto.org) websites and static sites.

## Why?

Existing ADS bibliography tools ([adstex](https://github.com/yymao/adstex), [filltex](https://github.com/dgerosa/filltex), [bibslurp](https://github.com/mkmcc/bibslurp)) are designed for LaTeX workflows. If you maintain an academic homepage with Quarto or another static site generator, you need something different:

- Fetch your **complete** publication list by ORCID, not from citation keys in a `.tex` file
- **Clean up** publisher artifacts (MathML markup, bulky fields) that break rendering
- **Tag** entries as refereed or non-refereed for display in separate sections
- Output a website-ready `.bib` file, not a LaTeX-specific one

## Installation

```bash
git clone https://github.com/michaelaye/ads-bib-tools.git
cd ads-bib-tools
pip install requests
```

Or just grab the script directly:

```bash
curl -O https://raw.githubusercontent.com/michaelaye/ads-bib-tools/main/update_bib.py
pip install requests
```

No other dependencies — `requests` is the only requirement.

## Setup

1. Get your ADS API token at <https://ui.adsabs.harvard.edu/user/settings/token>
2. Export it as an environment variable:

```bash
export ADS_API_TOKEN="your-token-here"
```

## Usage

### Basic usage

```bash
python update_bib.py --orcid 0000-0002-4088-1928
```

This writes `MyPublications.bib` with all publications linked to the ORCID, cleaned up and tagged.

### Options

```bash
python update_bib.py \
  --orcid 0000-0002-4088-1928 \
  --output publications.bib \
  --strip-fields file abstract note \
  --no-refereed-tag
```

| Flag | Description | Default |
|------|-------------|---------|
| `--orcid` | ORCID identifier (required) | — |
| `--output` | Output `.bib` file path | `MyPublications.bib` |
| `--strip-fields` | BibTeX fields to remove | `file abstract` |
| `--no-refereed-tag` | Skip refereed/non-refereed tagging | tagging enabled |

## What it does

1. **Fetches** all papers linked to your ORCID from NASA ADS, querying all three claim sources (`orcid_pub`, `orcid_user`, `orcid_other`) for completeness
2. **Exports** BibTeX via the ADS API
3. **Strips** bulky fields (`abstract`, `file` by default) to keep the `.bib` file lean
4. **Cleans** MathML markup that some publishers (e.g. Elsevier) embed in titles — converts to LaTeX notation
5. **Tags** each entry with `refereed` or `non-refereed` in the `keywords` field based on the ADS `property` metadata

The script is idempotent — running it again updates the file cleanly without duplicating tags.

## Integration with Quarto

The tagged `keywords` field works with these companion Quarto extensions:

- [chronobib](https://github.com/michaelaye/chronobib) — groups bibliography entries by year, with optional refereed/non-refereed tab splitting
- [highlight-author](https://github.com/michaelaye/highlight-author) — highlights a specific author's name in bibliography entries

Example using all three together:

```yaml
---
bibliography: MyPublications.bib
citeproc: false
filters:
  - michaelaye/highlight-author
  - michaelaye/chronobib
highlight-author: "Aye"
chronobib:
  split-keyword: refereed
---

::: {.panel-tabset}
## Refereed

::: {#refs-refereed}
:::

## Non-Refereed

::: {#refs-nonrefereed}
:::
:::
```

## Future additions

- **Citation metrics fetcher** — pull h-index, citation counts, and read counts from the ADS metrics API; output as JSON or YAML for a publications dashboard
- **New paper alerts** — compare current ADS results against the existing `.bib` file and report new entries; suitable for a CI cron job that auto-creates PRs with new publications
- **Co-author network** — extract co-author relationships across all papers and output as a graph for visualization (D3, Mermaid, etc.)
- **BibTeX linter/normalizer** — beyond MathML: normalize journal macros, fix encoding issues, standardize author name formats, deduplicate entries
- **ORCID completeness checker** — compare ADS results against your ORCID record and flag papers missing from either
- **Publication stats generator** — yearly paper counts, refereed ratio, journal distribution as a JSON/YAML file for Quarto to ingest

Contributions and ideas welcome — open an issue or PR.

## License

MIT
