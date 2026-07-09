<!--
SPDX-FileCopyrightText: © 2024 Tyler Nivin
SPDX-License-Identifier: MIT
-->

# Agent instructions — Ansible RPi Pi-hole

Deploy a recursive Pi-hole DNS server (Pi-hole + unbound via Podman) to a Raspberry Pi with Ansible.

Baseline guidance for AI coding agents working in this repo. Keep it current as the project
grows.

## The quality gate

One command runs every lint, format, license, and security check (the same set CI runs):

```console
uvx prek@0.4.8 run --all-files
```

Run it before you consider a change done; fix what it flags rather than suppressing it. Hooks
also run automatically on commit once `uvx prek@0.4.8 install` has been run. The version is
pinned to match CI exactly (`ci.yml`'s "Run prek hooks (same as local)" step) — Renovate bumps
it here and in CI together.

## Commits

Use **Conventional Commits** (`feat:`, `fix:`, `chore:`, `docs:`…). They drive automated
versioning and the changelog via release-please — the commit type is what decides the next
version, so it's not just style. `main` is protected (no direct commits); branch and open a PR.

## Where things live

- `.config/` — tool configuration (lint/format/release config lives here, not scattered at root).
- `.github/workflows/` — CI and the release pipeline.
- `playbooks/` / `inventory/` / `ansible.cfg` — the Ansible playbook project. Galaxy deps in `requirements.yml`.

## Ansible

A playbook project (`ansible.cfg` + `inventory/` + `playbooks/`). Galaxy content
dependencies live in `requirements.yml`, installed via `ansible-galaxy install -r requirements.yml`.
Linting is **ansible-lint** (it runs `--syntax-check` internally) and runs in the prek gate —
there is no `pyproject.toml`
or venv, so run tools with `uvx`/the prek hook, not `uv run`.

When you add tasks: use fully-qualified `ansible.builtin.*` module names, name every task and
play, and write YAML the repo's yamllint accepts (`true`/`false`, not `yes`/`no`; no bare
octals) so both yamllint and ansible-lint stay green.

## Licensing

Files carry SPDX headers (REUSE-compliant); the gate enforces it. When you add a file, give
it a header in the project's style or the `reuse` check will fail.
