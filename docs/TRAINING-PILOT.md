# Training pilot: free client and managed compute

The Apache-2.0 SDK is a free public client. Downloading or installing it does
not provide a training service, a compute allocation, a subscription, or
permission to spend money. A managed training pilot uses a separately
authorized DataLab deployment and its server-side project, resource and
commercial policies.

This guide describes a proposed integration boundary. It does not announce
an available public demo endpoint, pricing, a service SLA, or a completed
DataLab/Argus end-to-end integration.

## What is public

The SDK contains typed HTTP clients, bounded artifact I/O, Git metadata helpers
and telemetry. It does not contain the private DataLab Control Plane or Data
Plane, migrations, deployment topology, training engine, model weights,
datasets, or service credentials.

Keep private implementation and operational material out of public examples,
issues, logs and release packages. The SDK remains usable as a client against
a compatible, independently supplied gateway.

## Inert example profile

[training-pilot.profile.json](../examples/training-pilot.profile.json) is a
design example for a future caller-owned pilot wrapper. It is not a schema
implemented by the SDK, an API request body, or an executable configuration.
The current clients do not load it, enforce its limits, or submit work from it.

The example defaults to an unsubmitted local-tiny profile. Opening this profile,
installing the SDK, or running its tests does not submit managed training or
authorize paid compute. Local validation means checking configuration
only; even local training needs an explicitly supplied runner and workload.

Do not copy the profile into a request to an undocumented endpoint. A future
wrapper must translate an approved profile into the published server contract
and provide its own configuration validation and execution controls.

## Two different pilot modes

| Mode | Purpose | Required environment | What success establishes |
| --- | --- | --- | --- |
| Local tiny smoke | Exercise a small operator-provided workload on synthetic data | A separate local runner, compatible workload dependencies and enforced limits | Local execution and selected artifact checks only |
| Managed training | Request an approved workload through a compatible DataLab service | Authorized gateway, project, token, dataset/workload identifiers and server admission | Only the server actions and evidence actually observed |

The local mode is not an SDK training engine, a model-quality benchmark, a
production acceptance test, or proof of a Gateway/Control Plane/Data Plane
roundtrip. No local model implementation, weights or corpus are bundled here.
It must not silently switch to managed execution when a local prerequisite
is unavailable.

## Managed submission prerequisites

Before a future integration may submit managed work, the operator must provide:

1. A real HTTPS gateway and a scoped runtime credential. The reserved
   `example.invalid` URL and identifier placeholders in the example are not
   usable service configuration.
2. An existing authorized project and the exact compatible API/workload
   contract. Existing SDK `create_run()` and project-scoped `dispatch_run()`
   behavior must be checked against that server before claiming an end-to-end
   flow. Preserve documented legacy APIs; do not invent request fields.
3. Approved dataset/version and workload/image identifiers, with integrity and
   source lineage appropriate to the service contract.
4. An explicit device/resource request and bounded steps, duration and artifact
   sizes. The server must confirm support and enforce admission and quotas.
5. Explicit operator approval of a maximum charge and currency against the
   service's actual commercial terms. A null budget is incomplete configuration
   and must prevent managed submission. A client-side amount does not enforce a
   server-side spending cap.
6. A deliberate submit action after validation. `submission_enabled: false`
   in the example expresses the intended safe default; it is not an existing
   SDK enforcement mechanism.

This document does not define quote, payment, billing or cancellation APIs.
If the selected service lacks required spending, authorization or recovery
controls, the managed pilot remains blocked.

Read tokens from the runtime secret boundary, such as `ANIMUS_AUTH_TOKEN`.
Never put a live token in this JSON file, a URL, source control, printed
configuration, or an audit artifact.

## CPU, CUDA and MPS compatibility

A device name is a request, not proof of compatibility. The local-tiny example
requests CPU. Managed workloads must be admitted against the actual device,
OS/architecture, driver, framework/image, memory and project policy.

CUDA requires a compatible authorized GPU environment. MPS requires a
compatible Apple environment and framework; this guide does not claim a
managed MPS offering. CPU availability likewise does not establish that every
workload or model is supported.

The example permits no fallback. Unsupported devices must be rejected until
the operator chooses another approved profile. Do not silently change device,
model, precision, dataset, provider or spending envelope.

## Evidence and completion

Record only observed results: source/version identifiers, accepted configuration,
dataset and artifact hashes, run/attempt identity, execution environment, exits,
metrics, incomplete stages and limitations. Keep secrets and private server
implementation out of the report.

A successful SDK HTTP call proves only that call's observed response.
Best-effort telemetry delivery is not authoritative training completion.
Artifact digest checks detect mismatches against expected values; they do not
authenticate a worker or prove model quality.

A managed pilot needs separate positive and negative integration evidence,
including denial without entitlement, unsupported device, exhausted budget,
wrong project, invalid artifact digest and an unknown/lost worker outcome.
Mocked or local tests do not establish those server-side guarantees.

## Compatibility

This guide and profile are additive documentation. They do not change package
`animus-datalab`, import namespace `animus_sdk`, SDK version `1.2.0`, its
existing public methods, zero-runtime-dependency policy, or Apache-2.0 license.

See [architecture](ARCHITECTURE.md),
[compatibility](COMPATIBILITY.md), and [security](../SECURITY.md).
