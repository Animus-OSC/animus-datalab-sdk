# Contributing to Animus DataLab SDK

Thank you for contributing. This repository is an intentionally public integration boundary for Animus DataLab. Changes are reviewed for API compatibility, failure semantics, security, packaging, reproducibility, and disclosure safety as well as code correctness.

## Public boundary

This repository contains the public Python SDK and public compatibility documentation. It does not contain or depend on private server implementation details, production credentials, internal topology, private deployment manifests, customer data, or unreleased Animus core code.

- Server/API contracts are authoritative for HTTP paths, payloads, compatibility, and execution semantics.
- The SDK projects those contracts without silently widening authority.
- Private implementation details must not be copied into issues, pull requests, tests, fixtures, logs, screenshots, or documentation.
- Examples must use synthetic identifiers and non-routable/example endpoints.

## Development setup

From the repository root:

```bash
python -m pip install -e "python[dev]"
```

Run the local quality gates from `python/`:

```bash
python -m compileall -q src
python -m ruff check src tests
python -m mypy src/animus_sdk
python -m pytest --cov=animus_sdk --cov-branch --cov-report=term-missing --cov-fail-under=70
```

GitHub Actions additionally validates the supported CPython matrix, macOS/Windows smoke tests, and clean wheel installation.

## Change rules

### API and compatibility

For any change to HTTP paths, request/response models, authentication, retries, execution dispatch, datasets, experiments, artifacts, or telemetry:

1. identify the corresponding public contract/version boundary;
2. preserve documented compatibility or explicitly propose a version boundary;
3. add positive and negative tests for changed behavior;
4. document new failure, retry, redirect, or idempotency semantics;
5. update `docs/COMPATIBILITY.md` when the supported contract surface changes.

### Security-sensitive changes

Treat credentials, signing, redirects, headers, URL parsing, artifact paths, integrity checks, temporary files, error-body handling, and release workflows as security-sensitive surfaces.

Do not weaken validation or bounds merely to make an integration pass. Security regressions require explicit review and tests. Suspected vulnerabilities must follow `SECURITY.md` and use GitHub's private security-advisory flow instead of a public issue.

### Runtime dependencies

The SDK intentionally has zero runtime dependencies. Adding one changes a material deployment and supply-chain property and requires explicit justification, security review, and documentation.

### Large-file behavior

Artifact operations should remain streaming and bounded. Avoid whole-file buffering unless a documented hard size limit makes it safe and the tradeoff is justified.

### Telemetry

Telemetry must not make an application or training workload fail merely because observability delivery is unavailable. Queues, retries, drops, and shutdown behavior must remain bounded and observable.

## Pull requests

Keep PRs focused and explain:

- the user or integration problem;
- the public contract or compatibility boundary;
- failure/retry/redirect behavior;
- security and disclosure implications;
- tests added or changed;
- documentation/release impact.

A PR is ready to merge only when its exact head revision passes required CI and correctness/security review comments are resolved.

## Releases

Do not create or move release tags as part of a normal feature PR. Release identity and package publication follow `RELEASING.md`. Published versions and tags are immutable; defects are fixed forward with a new version.

## Style

Prefer small, explicit interfaces and predictable errors over convenience magic. Preserve request IDs and diagnostic context where safe. Make validation deterministic and test negative cases, not only happy paths.
