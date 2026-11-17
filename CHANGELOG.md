# Changelog

## Unreleased
- Fix: `remediate` with `--check` no longer fails; the re-audit is skipped in check mode.
- Simplified the audit logic (drift check, fail condition, `/proc/modules` parsing); no behaviour change.
- yamllint checks line length (120) and booleans again.
- README example uses `iamenr0s.kernel_hardening` with `become: true`.

## 1.0.0
- Split out of the iamenr0s.linux_hardening collection; no functional change.
