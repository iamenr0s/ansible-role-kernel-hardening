[![Molecule](https://github.com/iamenr0s/ansible-role-kernel-hardening/actions/workflows/molecule.yml/badge.svg)](https://github.com/iamenr0s/ansible-role-kernel-hardening/actions/workflows/molecule.yml) ![Ansible Role](https://img.shields.io/ansible/role/d/iamenr0s/ansible_role_kernel_hardening) [![CodeFactor](https://www.codefactor.io/repository/github/iamenr0s/ansible-role-kernel-hardening/badge)](https://www.codefactor.io/repository/github/iamenr0s/ansible-role-kernel-hardening)

# Ansible Role: Kernel Hardening

> Standalone copy of `kernel_hardening` from the [iamenr0s.linux_hardening](https://github.com/iamenr0s/linux-hardening-collection) collection. Variables keep the collection-wide `linux_hardening_*` prefix.

Sole owner of kernel module blacklisting in this collection. Writes one drop-in, `/etc/modprobe.d/99-linux-hardening.conf`, with `install <module> /bin/true` and `blacklist <module>` for rarely needed modules. Sysctl settings belong to `sysctl_hardening`.

## What it disables
Our own selection (not a verified CIS mapping): `cramfs freevxfs hfs hfsplus jffs2 dccp rds tipc`. `cis_level2` adds `sctp udf usb-storage` (usb-storage blocks USB disks). Exact list: `vars/main.yml`. Keep a module with `linux_hardening_kernel_modules_allow`, add one with `linux_hardening_kernel_modules_extra`.

## Modes
`audit` (default) is read-only and sets `linux_hardening_kernel_findings` (`key, expected, actual, fixable`). `remediate` needs `linux_hardening_confirm_remediation: true`.

## Safety
- Modules that are already loaded are never unloaded; they are reported (`fixable: false`) and the drop-in takes effect for them after a reboot. They do not fail the run after remediation.
- Disabling a module affects only future loads, so remediation cannot break a running system. A later reboot can: allow any module your hardware, filesystems or workloads need (`sctp` for telecom, `udf` for optical media, `usb-storage` for USB disks).

## Example
```yaml
- hosts: all
  roles:
    - role: iamenr0s.linux_hardening.kernel_hardening
      vars:
        linux_hardening_mode: remediate
        linux_hardening_confirm_remediation: true
        linux_hardening_kernel_modules_allow: [udf]
```

## Verification
Molecule `default` (allow-list, extra, idempotence, re-audit), `strict`, `audit` (writes nothing), `refuse`. Tested: AlmaLinux 9 only so far.

## Rollback
Delete the drop-in (a backup of any earlier version is kept when `linux_hardening_backup` is true).

## Known gaps
- No module loading or options (the old `ansible-role-kernel-configuration` loader is not ported); no initramfs rebuild, so a module loaded in early boot may stay available until it is rebuilt.
- Containers report the host's loaded modules, so loaded-module findings there reflect the host.
- Boot parameters, `ptrace`/`kptr` (sysctl) and CIS IDs are out of scope or unmapped.

CI & Release (maintainers)
----------------------------
A single workflow (`.github/workflows/molecule.yml`) runs lint and the full Molecule distro matrix on pushes to `main`, PRs, and `v*` tags. On `v*` tags, a `release` job publishes to Ansible Galaxy after all tests pass.

One-time setup:

```bash
# Galaxy publishing key (environment-scoped, get it from galaxy.ansible.com/ui/token)
gh secret set GALAXY_API_KEY --repo iamenr0s/ansible-role-kernel-hardening

# Code scanning notifications (Slack webhook URL; for Discord append /slack to the webhook URL)
gh secret set SECURITY_ALERT_WEBHOOK --repo iamenr0s/ansible-role-kernel-hardening
```

`.github/workflows/code-scanning-notify.yml` polls the code-scanning API every 6 hours and posts new or updated open alerts to that webhook (GitHub Actions cannot trigger on `code_scanning_alert` directly).

To release: tag a commit `vX.Y.Z` and push the tag — CI gates the Galaxy publish.

## Contributing

Contributions are welcome — see [CONTRIBUTING.md](CONTRIBUTING.md) for the
local pipeline commands and pull request checklist. This project follows the
[Contributor Covenant Code of Conduct](CODE_OF_CONDUCT.md).

## Security

See [SECURITY.md](SECURITY.md) — GitHub private vulnerability reporting, no
public issues for security bugs.

## Changelog

See [CHANGELOG.md](CHANGELOG.md) for version history and release notes.

## License

This project is licensed under the [MIT License](LICENSE).

## Author Information

Author: iamenr0s

Galaxy: `iamenr0s.ansible_role_kernel_hardening`
