# Architecture

## System boundary

Animus DataLab is a governed ML infrastructure platform. Animus Link is a separate managed-access product in the same Animus portfolio; the two do not share an application runtime.

The Python SDK belongs to DataLab. It is the client-side projection used by CI systems, training containers and operator tooling to interact with the public DataPilot gateway contract.

```text
CI / training / operator tooling
             |
       animus-datalab SDK
             |
        DataPilot Gateway
             |
   +---------+----------+
   |                    |
Dataset Registry    Experiments
                        |
                 governed run state
                        |
              project-scoped dispatch
                        |
               isolated execution
```

This diagram is intentionally limited to the public integration boundary. Private service topology, deployment details, internal repository structure, credentials, and unreleased Animus core implementation are outside the scope of this repository.

## Authority

The SDK is not an independent source of API truth. Canonical external contracts are owned by DataLab and maintained in a separately governed contract source. SDK methods are reviewed as projections of those published contract versions rather than as a copy of private implementation.

The 1.2 SDK line targets Dataset Registry `0.2.x` and Experiments `0.3.x`.

## Execution model

The platform owns authoritative metadata, policy decisions, run identity, auditability and artifact mediation. User workloads execute behind the governed execution boundary. The public SDK expresses the execution transition as an explicit project-scoped dispatch of an existing run.

`ExperimentsClient.execute_run()` maps a legacy compatibility endpoint and must not be used as the architectural model for new integrations. New integrations use `create_run()` followed by `dispatch_run()`.

## Runtime strategy

The SDK intentionally has zero runtime dependencies and ships as a universal pure-Python wheel. Network and file I/O dominate its normal workload, so native compilation is not a default optimization. Rust/PyO3 `abi3` extensions are reserved for measured CPU-bound kernels.

## Failure model

Transport failures are normalized into `AnimusAPIError`; response bodies are bounded; URL/header inputs are validated; large files are streamed; downloads are written atomically and may be size- and digest-constrained. Telemetry is best-effort and bounded so observability failure cannot crash a training workload.
