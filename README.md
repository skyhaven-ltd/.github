# .github

Shared community health repository for the Sky Haven organisation. It provides
the organisation profile landing page, default issue and pull-request templates,
shared automation, and the canonical engineering standards that repositories
inherit when they do not define their own.

## Contents

- [`profile/README.md`](./profile/README.md) — organisation landing page.
- [`standards/`](./standards/) — canonical engineering standards.
  - [`repo-naming.md`](./standards/repo-naming.md) — repository naming standard
    (human-readable).
  - [`repo-naming.spec.yml`](./standards/repo-naming.spec.yml) — machine-readable
    source of truth, enforced by `infra-github-platform`.
- `.github/ISSUE_TEMPLATE/` and `.github/PULL_REQUEST_TEMPLATE/` — inherited
  templates.
