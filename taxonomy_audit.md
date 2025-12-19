# Algokit Repository Taxonomy & Alignment Audit

## 1. Core Libraries (*)
These are low-level dependencies or SDKs meant to be imported by other projects.

| Repo Name | Role | Justification | Alignment Priority |
| :--- | :--- | :--- | :--- |
| `algokit-utils-py` | Core Python utilities for Algorand development. | Contains [pyproject.toml](cci:7://file:///Users/arthurrabelo/Documents/temp/consistent_repos/algokit-cli/pyproject.toml:0:0-0:0) and is a dependency for other Python projects. Active decoupling work observed in `decoupling-account-refactor`. | **High** |
| `algokit-utils-ts` | Core TypeScript utilities for Algorand development. | Contains [package.json](cci:7://file:///Users/arthurrabelo/Documents/temp/consistent_repos/puya-ts/package.json:0:0-0:0) and is a dependency for other TS projects. Active decoupling work observed in `spike/decoupling-algorand-client`. | **High** |
| `algokit-subscriber-py` | Python library for subscribing to Algorand events. | Library structure with [pyproject.toml](cci:7://file:///Users/arthurrabelo/Documents/temp/consistent_repos/algokit-cli/pyproject.toml:0:0-0:0), focused on a specific utility function. | **High** |
| `algokit-subscriber-ts` | TypeScript library for subscribing to Algorand events. | Library structure with [package.json](cci:7://file:///Users/arthurrabelo/Documents/temp/consistent_repos/puya-ts/package.json:0:0-0:0), focused on a specific utility function. | **High** |
| `algorand-python-testing` | Python testing utilities for Algorand. | specialized testing library, likely used by `algokit-utils-py` consumers or internally. | **High** |
| `algorand-typescript-testing` | TypeScript testing utilities for Algorand. | specialized testing library, likely used by `algokit-utils-ts` consumers or internally. | **High** |

### Alignment North Star (Core Libraries)
1.  **Strict Semantic Versioning**: All libraries must strictly adhere to SemVer, with automated release notes generation.
2.  **Unified Type/Lint Config**: All libraries must share a common base configuration for linting (e.g., `eslint-config-algokit`, `ruff` config) to ensure code style consistency.
3.  **Decoupled Architecture**: Utilities should be decoupled from specific client implementations (as seen in the ongoing decoupling work) to allow for easier testing and mocking.

---

## 2. User-Facing Apps (**)
These are standalone tools or services (CLI, APIs, UI).

| Repo Name | Role | Justification | Alignment Priority |
| :--- | :--- | :--- | :--- |
| `algokit-cli` | The main command-line interface for Algokit. | Contains entry points (`algokit`), complex logic, and is the primary user interface. | **High** |
| `algokit-avm-vscode-debugger` | VS Code extension for debugging AVM. | VS Code extension structure ([package.json](cci:7://file:///Users/arthurrabelo/Documents/temp/consistent_repos/puya-ts/package.json:0:0-0:0) with `engines.vscode`), user-facing UI tool. | **Med** |
| `algokit-dispenser-api` | API service for dispensing testnet funds. | Service structure ([sst.config.ts](cci:7://file:///Users/arthurrabelo/Documents/temp/consistent_repos/algokit-dispenser-api/sst.config.ts:0:0-0:0), `stacks`), deployable application. | **Med** |
| `algokit-client-generator-py` | Python client code generator. | Generates code for users, likely invoked via CLI or standalone. | **Med** |
| `algokit-client-generator-ts` | TypeScript client code generator. | Generates code for users, likely invoked via CLI or standalone. | **Med** |

### Alignment North Star (User-Facing Apps)
1.  **Consistent CLI/API UX**: All user-facing tools should share consistent argument parsing, logging patterns, and error output formats.
2.  **Telemetry & Analytics**: Standardized opt-in telemetry to understand user usage patterns across all tools.
3.  **Robust E2E Testing**: Apps must have end-to-end tests that simulate real user workflows (e.g., using the CLI to create and deploy an app).

---

## 3. Project Templates
Repositories used as "blueprints" for `algokit init`.

| Repo Name | Role | Justification | Alignment Priority |
| :--- | :--- | :--- | :--- |
| `algokit-example-gallery` | Collection of example projects and templates. | Contains `algokit-templates` directory, clearly serving as a source for examples. | **Low** |

### Alignment North Star (Project Templates)
1.  **Regular CI Validation**: Templates must be built and tested in CI on a schedule to ensure they don't go stale against new library versions.
2.  **Minimal Dependencies**: Keep templates lean to reduce initial setup time and potential conflicts.
3.  **Clear "Getting Started"**: Every template must have a standardized [README.md](cci:7://file:///Users/arthurrabelo/Documents/temp/consistent_repos/puya-ts/README.md:0:0-0:0) section explaining how to run it immediately.

---

## 4. Support Utilities
Small, internal-only tools or niche plugins.

| Repo Name | Role | Justification | Alignment Priority |
| :--- | :--- | :--- | :--- |
| `algokit-utils-ts-debug` | Debugging utilities for TypeScript. | Specialized, likely internal or niche utility, separate from the main utils. | **Low** |

### Alignment North Star (Support Utilities)
1.  **Low Maintenance Overhead**: Automate dependency updates and releases to minimize manual effort.
2.  **Clear Scope**: Explicitly define the limited scope to prevent feature creep (should this be merged into Core Libs?).
3.  **Internal Documentation**: Ensure internal docs are sufficient for maintainers, even if public docs are minimal.

---

## 5. New Category: Compiler/Language Core
Tools related to language compilation or core language features.

| Repo Name | Role | Justification | Alignment Priority |
| :--- | :--- | :--- | :--- |
| `puya-ts` | TypeScript to TEAL compiler/transpiler. | Complex structure (`packages` monorepo), involves compilation logic, distinct from standard libs or apps. | **High** |

### Alignment North Star (Compiler/Language Core)
1.  **High Correctness Standards**: Extensive fuzzing and property-based testing are required due to the critical nature of compilation.
2.  **Language Spec Compliance**: Strict adherence to the AVM (Algorand Virtual Machine) specification.
3.  **Performance Benchmarking**: Compilation speed and output size must be tracked and optimized.

---

## Logical Batches for Migration
Grouped for migration to Trunk-Based Development.

### Batch 1: The Core Foundation (High Impact, High Risk)
*   `algokit-utils-py`
*   `algokit-utils-ts`
*   `algokit-subscriber-py`
*   `algokit-subscriber-ts`
*   `algorand-python-testing`
*   `algorand-typescript-testing`

**Rationale**: These are dependencies for everything else. Stabilizing their branching strategy and CI first is critical. The "decoupling" work should be merged or stabilized before full migration if possible, or migrated as part of the process.

### Batch 2: The Compiler & Critical Tools (High Impact, Med Risk)
*   `puya-ts`
*   `algokit-cli`

**Rationale**: `puya-ts` is complex but standalone. `algokit-cli` consumes the core libs. Migrating these after core libs ensures they consume the stable new versions.

### Batch 3: Generators & Services (Med Impact, Low Risk)
*   `algokit-client-generator-py`
*   `algokit-client-generator-ts`
*   `algokit-dispenser-api`
*   `algokit-avm-vscode-debugger`

**Rationale**: These are important but failures here are less catastrophic than in core libs. They can follow the patterns established by Batch 1 & 2.

### Batch 4: Templates & Niche Utils (Low Impact, Low Risk)
*   `algokit-example-gallery`
*   `algokit-utils-ts-debug`

**Rationale**: Lowest risk. Can be migrated last or whenever convenient.
