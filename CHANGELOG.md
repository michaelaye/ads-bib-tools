# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [0.1.0] - 2026-03-03

### Added

- `update_bib.py` — fetch complete bibliography from NASA ADS by ORCID.
- Query all three ADS ORCID claim sources (`orcid_pub`, `orcid_user`, `orcid_other`) for completeness.
- Strip configurable BibTeX fields (`abstract`, `file` by default) to keep output lean.
- Convert publisher MathML markup (e.g. from Elsevier) to LaTeX notation.
- Tag each entry with `refereed` or `non-refereed` keyword based on ADS property metadata.
- Idempotent keyword tagging — safe to re-run without duplicating tags.
- CLI interface with `--orcid`, `--output`, `--strip-fields`, and `--no-refereed-tag` options.

[0.1.0]: https://github.com/michaelaye/ads-bib-tools/releases/tag/v0.1.0
