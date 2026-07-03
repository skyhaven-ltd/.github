# Repository Naming Standard

The canonical naming convention for every repository in the Sky Haven
organisation. It is designed so that a name can be generated mechanically:
answer four questions in order and the name falls out, with no judgement calls.

The machine-readable source of truth is [`repo-naming.spec.yml`](./repo-naming.spec.yml),
which the repository provisioning in
[`infra-github-platform`](https://github.com/skyhaven-ltd/infra-github-platform)
enforces at `terraform plan`. If prose and spec ever disagree, the spec wins.

## Format

    <type>-<domain>-<component>[-<qualifier>]

All lowercase kebab-case. Tokens are separated by `-`. The qualifier is the only
optional token.

## How to name a repository

Work through the four steps in order.

### Step 1 — Pick the type

The type is the repository's **primary deliverable** — its reason to exist.
Exactly one applies. Walk the list top to bottom and take the first match.

1. Does it **serve requests or users** (an API, a web app, a running service)? → **`app`**
   Includes an app that carries its own deployment infrastructure, and a service
   that serves an ML model over an API.
2. Does it **move, transform, or analyse data** (ETL, dbt, analytics, model training)? → **`data`**
3. Is it a **reusable package other repos depend on** (an IaC module or a code library)? → **`module`**
4. Is it **reusable CI/CD** consumed by other repositories? → **`pipeline`**
5. Is it **infrastructure with its own lifecycle** or shared by many consumers
   (landing zones, shared platform, environment compositions)? → **`infra`**
6. Is it **prose only**? → **`docs`**

Tie-breakers:

- **app vs infra** — infrastructure that deploys only one app and ships on the
  same cadence stays inside that `app` repo. `infra` is for infrastructure with
  an independent lifecycle or multiple consumers.
- **data vs app** — the job that *trains* a model is `data`; the service that
  *serves* it is `app`.
- **pipeline vs data** — `pipeline` is CI/CD only; data/ETL pipelines are `data`.

If you reach for a type not in the list, it folds into one of the six:

| You were thinking… | Use |
| ------------------ | --- |
| `solution` (app + infra together) | the primary deliverable, usually `app` |
| `ops` runbooks / tooling | `docs`, `app`, or `pipeline` |
| `policy` / guardrails | `infra` (deployed) or `module` (reusable) |
| `config` / GitOps desired state | `infra` |
| `template` / `image` / `spec` | `module` |

### Step 2 — Pick the domain

The domain is a **controlled vocabulary**. Pick one; do not invent.

| Domain | For |
| ------ | --- |
| `braveart`, `certwatch`, `cvengine`, `powertoggle`, `learning` | products |
| `landingzone` | Azure foundation |
| `github` | GitHub org platform |
| `homelab` | homelab infrastructure (Terraform / Ansible / Kubernetes) |
| `developer` | developer tooling and configuration |
| `engineering` | shared / cross-cutting / meta (standards, org-wide templates) |

Need a domain that isn't listed? Add it by PR to
[`repo-naming.spec.yml`](./repo-naming.spec.yml) — that is the only way a new
domain becomes valid.

### Step 3 — Name the component

The specific part of the domain. One or more kebab words
(`core`, `web`, `private-endpoint`, `app-service`). Rules:

- Use the most specific accurate noun; avoid repeating the type or domain.
- Multi-word components are allowed — the parsing rule below keeps names
  unambiguous.

### Step 4 — Add the qualifier

The qualifier is a **controlled technology vocabulary**:
`terraform`, `bicep`, `ansible`, `kubernetes`, `helm`, `docker`, `node`,
`dotnet`, `python`, `powershell`, `actions` (extend by PR to the spec).

- **`module` and `pipeline` — the qualifier is mandatory.** Consumers must know
  the technology: `module-landingzone-networking-terraform`.
- **Every other type — omit it**, unless two repositories would otherwise share
  an identical name, in which case add the qualifier that distinguishes them.

## Parsing rule

Names are deterministically decomposable. Split on `-`: the first token is the
type, the second is the domain. **If the final token is a registered qualifier,
it is the qualifier and every token between the domain and it is the component;
otherwise there is no qualifier and all trailing tokens are the component.**

    module-landingzone-private-endpoint-terraform
      type=module  domain=landingzone  component=private-endpoint  qualifier=terraform

    app-cvengine-web
      type=app  domain=cvengine  component=web  qualifier=(none)

## Examples

| Name | type | domain | component | qualifier |
| ---- | ---- | ------ | --------- | --------- |
| `app-cvengine-web` | app | cvengine | web | — |
| `app-cvengine-portfolio` | app | cvengine | portfolio | — |
| `infra-landingzone-core` | infra | landingzone | core | — |
| `infra-homelab-config` | infra | homelab | config | — |
| `module-landingzone-networking-terraform` | module | landingzone | networking | terraform |
| `pipeline-engineering-build-node` | pipeline | engineering | build | node |
| `pipeline-engineering-github-actions` | pipeline | engineering | github | actions |
| `data-cvengine-model-train` | data | cvengine | model-train | — |
| `docs-engineering-standards` | docs | engineering | standards | — |

## Governance

- The vocabularies (types, domains, qualifiers) and the grammar live in
  [`repo-naming.spec.yml`](./repo-naming.spec.yml). Change the standard by
  changing the spec.
- New repositories are validated against the grammar at `terraform plan` in
  `infra-github-platform`. Non-conforming names fail the plan.
- The special `.github` repository is exempt.
- A small set of pre-existing names is grandfathered until renamed; the
  allowlist lives in the spec and is not for new repositories.
