# Repository Guidelines

## Project Structure & Module Organization

This repository contains Mihomo/Clash rule providers and proxy-provider templates. `rules/*.list` holds domain and IP rules grouped by service. `proxy-providers/` holds remote subscription provider templates; `proxies/` holds local node templates. `config.example.yaml` defines local providers, proxy groups, proxy-provider references, and rule precedence for merging into an existing Mihomo configuration. `README.md` documents routing policies and setup. Keep provider filenames and keys aligned, such as `rules/GameCDN.list` and `GameCDN`. Do not add generated Clash state, proxy credentials, or subscription URLs.

## Development Commands

No build or dependency installation is required. Use `rg -n "DOMAIN-SUFFIX,example.com" rules` to find overlapping entries before editing. After merging the example into a complete local configuration, run `mihomo -t -f your-config.yaml` to validate syntax, provider loading, and proxy-provider references. Use `git status --short` to review all changed and untracked rule and provider files before committing.

## Coding Style & Naming Conventions

Use UTF-8, LF line endings, and a final newline. Keep one rule per line, use `#` comments for service sections, and separate sections with blank lines. Match existing `PascalCase.list` filenames and provider keys. Use `DOMAIN` for one host, `DOMAIN-SUFFIX` for a domain and its subdomains, and `DOMAIN-KEYWORD` only when broad matching is intentional. Add `no-resolve` to `IP-CIDR` and `IP-CIDR6` entries. Preserve critical ordering: local traffic first, direct CDN rules before API rules, `AI` before `Google`, and mainland China direct rules before the final fallback.

## Testing Guidelines

There is no unit-test framework. Check every edited list for malformed or duplicate entries, verify every provider path exists, and ensure all `RULE-SET` references and `proxy-providers` `use:` entries have matching providers. Run `mihomo -t` against a complete configuration when Mihomo is available. Functionally test one representative endpoint per changed service, especially CDN downloads, AI routing, and Bahamut's Taiwan-only routing.

## Commit & Pull Request Guidelines

The repository has no established commit convention yet. Use concise imperative subjects, for example `Add developer service rules`, and keep each commit focused on one routing change. Pull requests should identify affected services, explain policy or ordering changes, and list validation performed. Include representative test domains and mention any new GeoSite, GeoIP, or external provider dependency. Never include private node information or subscription credentials.
