CRE SDK Go → TypeScript Porting Requirements
Purpose
Re‑implement the existing Go‑based CRE SDK in TypeScript so that Node.js developers can build and test CRE capabilities without depending on Go. The port must preserve feature parity with the Go implementation while adopting idiomatic TypeScript patterns and a Node‑oriented build/test pipeline.

Scope
Components to Port
Go Source Area	Description	TypeScript Destination
capabilities/blockchain/evm, capabilities/networking/http, capabilities/scheduler/cron	Capability clients, triggers, mocks, and helper utilities.	src/capabilities/* modules with accompanying unit tests and mocks.
cre/	Core runtime, WASM helpers, and test utilities.	src/core/* (runtime abstractions) and src/testutils/*.
generator/	Code generation logic for building SDK artifacts and capability templates.	tools/generator/* using protobufjs or similar, with CLI wrappers.
cmd/newcapability	CLI templates for bootstrapping new capabilities.	tools/cli/new-capability/* using Node CLI frameworks.
internal/sdkimpl	Internal SDK implementation details.	Internal helpers under src/internal/* (not exported publicly).
standard_tests/ & internal_testing/	Acceptance and regression tests validating capability behavior.	Rewritten tests under test/standard/* with Node testing framework (e.g., Jest, Mocha).
Out of Scope
Rewriting external blockchain clients or consensus algorithms.

Designing new capabilities beyond those existing in the Go SDK.

Supporting browser environments (initial release targets Node.js LTS).

Target Architecture Overview
Package Layout

src/ – SDK runtime and capability modules.

tools/ – Generators and CLI utilities.

test/ – Unit and integration tests.

examples/ – Sample applications.

Language & Tooling

TypeScript 5.x, ES2020 target.

protobufjs for ProtoBuf message handling.

ts-node for development, esbuild or tsc for bundling.

Testing with Jest (preferred) or Mocha + Chai.

Linting with ESLint + Prettier.

Concurrency & Asynchrony

Replace Go routines/channels with async/await and event emitters.

Use Node streams or worker threads only where necessary.

Configuration & Packaging

Distribute as npm package @cre/sdk.

Provide compiled JavaScript and type definitions.

Ensure tree‑shakable modules and ESM/CJS compatibility.

Team Roles & Responsibilities
Role	Primary Duties
Technical Lead / Architect	Finalize module boundaries, data flow, and async patterns. Approve API surface. Coordinate TypeScript vs. Go parity.
TypeScript Capability Engineers	Port each capability (blockchain/evm, networking/http, scheduler/cron) into modular TS packages. Implement mocks and unit tests.
Runtime/Platform Engineer	Recreate core runtime (cre/) abstractions, WASM helpers, and internal SDK implementations. Ensure extensibility for future capabilities.
Protocol Buffers Specialist	Extract or recreate .proto definitions from existing Go artifacts, generate TS message classes, and maintain generator CLI.
Tooling & DevOps Engineer	Set up build system, package publishing, linting, and CI workflows. Provide scripts mirroring Go tools (e.g., generator/, cmd/newcapability).
QA / Test Engineer	Translate standard_tests/ and internal_testing/ suites into Jest/Mocha, create integration tests, and maintain coverage metrics.
Technical Writer	Document API usage, capability guides, and migration notes. Maintain README and examples.
Project Manager	Coordinate cross‑role deliverables, maintain backlog, track milestones, and manage external dependencies (e.g., proto specs).

Acceptance Criteria
Feature parity: APIs and behavior match Go SDK for listed capabilities.

90% unit test coverage for core modules; all integration tests pass.

Generated TypeScript types and runtime modules correctly interoperate with Node.js.

Documentation and examples allow new users to build a sample capability end‑to‑end within one hour.

CI pipeline runs lint, build, and test on each pull request and publishes tagged releases.

Risks & Open Questions
Proto Source Availability – .proto files are absent in the Go repo. Decide whether to reverse‑engineer from .pb.go or retrieve from upstream.

WASM Execution – Ensure Node's WASM support is sufficient for existing cre/wasm helpers; otherwise, adjust runtime assumptions.

Blockchain Dependencies – EVM module may rely on Go‑specific crypto libraries; suitable JS equivalents must be validated.

Performance Differences – Async Node implementations might behave differently from Go’s concurrency model; monitor for regressions.

Resource Allocation – Multiple roles are required concurrently; schedule may slip if hiring or availability changes.

Appendices
A. Development Environment
Node.js ≥ 18

pnpm or npm for package management

Docker (optional) for reproducible dev environments

B. Coding Standards
Follow ESLint/Prettier rules (strict mode, no implicit any).

Public APIs must be fully typed and documented with TSDoc.
