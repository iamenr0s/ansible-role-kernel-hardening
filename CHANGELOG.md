# Changelog

## Unreleased
- Change: dropped the `linux_hardening_mode` assert that repeated `meta/argument_specs.yml` (which already rejects other values).
- Change: task names in sub-task files are prefixed.
- Docs: README adds a Role Variables table, idempotency and check-mode notes.
- CI: every platform in `meta/main.yml` gates the release (no `continue-on-error` legs); lint runs the pinned pre-commit hooks; pre-commit adds gitleaks; actions are pinned by commit SHA.
- Fix: `remediate` with `--check` no longer fails; the re-audit is skipped in check mode.
- Simplified the audit logic (drift check, fail condition, `/proc/modules` parsing); no behaviour change.
- yamllint checks line length (120) and booleans again.
- README example uses `iamenr0s.kernel_hardening` with `become: true`.

## 1.0.0
- Split out of the iamenr0s.linux_hardening collection; no functional change.
