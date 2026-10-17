# Lambda Constellation

**An experimental, extensible browser-based volunteer computing platform powered by WebAssembly and independent search-space exploration.**

> **Status:** Work in progress as part of an undergraduate thesis (TCC). The initial monorepository structure is in place; the API, solvers, and execution workflows are still under development. The interfaces described below reflect the intended architecture, not features that are already available.

## Overview

**Lambda Constellation** aims to harness the computing power of volunteer devices through web browsers. Instead of requiring users to install a native client, the platform is designed to run algorithms compiled to **WebAssembly (WASM)** in the background using **Web Workers**, keeping the user interface responsive.

The platform is conceived as a **reusable repository template**. Researchers should be able to adapt scientific algorithms with the assistance of **coding agents**, without rebuilding the web application or the shared-state infrastructure.

The research focuses on strategies for exploring large state spaces—including deterministic and nondeterministic searches—in a volunteer computing environment with **minimal coordination**.

## Architectural Principles

- **Independent execution:** Each volunteer explores the search space independently. Web Workers in different browsers do not communicate directly.
- **Client-side branch selection:** Search regions are selected by clients, potentially through randomized strategies, without a central task scheduler.
- **Shared global state:** The API accepts contributions and makes the aggregated state available to participants.
- **Eventual consistency:** New contributions do not have to appear immediately in subsequent reads. Clients must tolerate stale state.
- **Minimal HTTP interface:** The planned API exposes only two operations: `GET /` (read available state) and `POST /` (submit a contribution).
- **Decoupled algorithms:** Search logic lives in Rust libraries compiled to WASM. The web interface and API do not need to know their internal implementations.
- **Agent-assisted development:** Documentation and standardized commands help coding agents integrate new scientific problems.

## Conceptual Architecture

```text
                  +---------------------------+
                  |     ASP.NET Core API      |
                  |    Shared global state    |
                  |     GET /    POST /       |
                  +-------------+-------------+
                                ^
                          HTTP GET / POST
                                |
             +------------------+------------------+
             |                  |                  |
       +-----+------+     +-----+------+     +-----+------+
       |  Browser A |     |  Browser B |     |  Browser C |
       |   Preact   |     |   Preact   |     |   Preact   |
       | Web Worker |     | Web Worker |     | Web Worker |
       |    WASM    |     |    WASM    |     |    WASM    |
       +------------+     +------------+     +------------+

           Independent exploration; no worker-to-worker communication
```

The backend **does not** coordinate worker execution, assign subtrees, or push real-time updates. Each client reads shared state when needed, searches locally, and publishes contributions it considers relevant. Polling frequency and contribution criteria are implementation and research decisions.

Eventual consistency **does not** mean accepting unvalidated results or dropping contributions. The system still needs rules for merging data, handling duplicates, and preserving scientific problem invariants.

## Technology Stack

| Layer | Technology | Purpose |
| --- | --- | --- |
| Web application | **Preact + TypeScript + Vite** | Volunteer interface and shared-state visualization |
| In-browser computation | **Web Workers + WebAssembly** | CPU-intensive execution without blocking the UI |
| Solvers | **Rust** | Search strategies compiled to WASM |
| Backend | **ASP.NET Core / .NET** | Contribution submission and shared-state retrieval |
| Local development | **GNU Make + Docker Compose** | Automation and supporting services |
| Module distribution | **npm / GitHub Packages** *(planned)* | Publishing and consuming WASM packages |

**Preact** was chosen to keep the web application lightweight. Computational performance, however, primarily depends on the algorithms, WASM modules, and Web Worker execution model; performance improvements must be measured experimentally.

## Repository Structure

```text
lambda-constellation/
├── apps/
│   ├── api/                        # ASP.NET Core API
│   └── web/                        # Preact + Vite frontend
├── wasm/
│   ├── deterministic-solver/       # Deterministic search in Rust
│   └── non-deterministic-solver/   # Nondeterministic search in Rust
├── dev-ops/
│   ├── compose.yaml                # Development services
│   └── docker/                     # Container configurations
├── docs/
│   ├── agents/                     # Coding-agent guidelines
│   ├── architecture/               # Architectural documentation
│   ├── decisions/                  # Architecture Decision Records (ADRs)
│   ├── guides/                     # Usage and integration guides
│   └── specifications/             # Contracts and specifications
├── scripts/                        # Supporting scripts
├── AGENTS.md                       # Repository-wide agent instructions
├── CHANGELOG.md
├── CONTRIBUTING.md
├── LICENSE
├── Makefile                        # Local development entry point
└── README.md
```

The two WASM packages represent **search strategies**, not two exclusive categories of scientific problems. Determinism should not be confused with the ability to find or prove a global optimum.

## Intended Contribution Flow

1. A participant opens the web application and opts in to volunteer computing.
2. The application retrieves the available state through `GET /`.
3. A Web Worker loads the appropriate WASM module and begins local exploration using the information available.
4. The solver searches for candidate solutions or new states according to its strategy, without exchanging messages with other volunteers.
5. When appropriate, the client submits its findings through `POST /`.
6. The API processes and merges the contribution according to the shared-state rules; subsequent reads may observe it later.

This flow is **conceptual** and may be refined during implementation. Request and response schemas will be documented under `docs/specifications/`.

## Local Development

### Prerequisites

- Git
- A .NET SDK compatible with `apps/api/`
- Node.js and pnpm for the web application
- Rust and Cargo for the solvers
- Rust-to-WebAssembly compilation tools (to be selected and documented)
- GNU Make
- Docker with Compose, if local supporting services are used

### Clone the Repository

```bash
git clone https://github.com/YOUR-USERNAME/lambda-constellation.git
cd lambda-constellation
```

> Replace `YOUR-USERNAME` with the actual GitHub repository owner.

### Run the Initial Projects

While the Makefile and WASM integration are being developed, the projects can be run or checked separately:

```bash
# Frontend (terminal 1)
cd apps/web
pnpm install
pnpm dev
```

```bash
# Backend (terminal 2, from the repository root)
dotnet run --project apps/api/api.csproj
```

```bash
# Check Rust projects (from the repository root)
cargo check --manifest-path wasm/deterministic-solver/Cargo.toml
cargo check --manifest-path wasm/non-deterministic-solver/Cargo.toml
```

These commands assume the generated projects still use the names and configuration shown in the initial structure. **They do not yet provide a functioning distributed computing workflow.** Compiling and importing the WASM packages into the frontend are upcoming milestones.

The root `Makefile` is intended to become the unified development interface. See `make help` for its available targets **once implemented**.

## Roadmap

- [x] Establish the initial monorepository and application scaffolding
- [ ] Specify the global state and HTTP contribution schemas
- [ ] Implement `GET /` and `POST /`
- [ ] Compile both Rust solvers to WebAssembly
- [ ] Run and manage WASM modules in Web Workers
- [ ] Integrate the frontend with the API
- [ ] Automate builds and tests through Make
- [ ] Publish WASM packages to GitHub Packages
- [ ] Document agent-assisted algorithm integration
- [ ] Conduct performance, concurrency, and convergence experiments

## Research Questions and Evaluation

The experimental evaluation aims to investigate:

- How independent search behaves as the number of volunteers increases
- Communication overhead relative to useful computation
- The impact of overlapping searches and randomized search-region selection
- The quality of results produced by different search strategies
- Shared-state convergence under delayed, duplicate, or out-of-order contributions
- Whether coding agents can integrate new algorithms without modifying the underlying platform infrastructure

Finding local minima or high-quality solutions **does not, by itself, prove global optimality**. Correctness and completeness guarantees depend on the chosen search strategy and must be stated for each scientific problem.

## Documentation and Contributing

- [`AGENTS.md`](AGENTS.md): repository-wide instructions for coding agents
- [`docs/architecture/`](docs/architecture/): platform architecture
- [`docs/specifications/`](docs/specifications/): system and solver contracts
- [`docs/guides/`](docs/guides/): development and integration procedures
- [`CONTRIBUTING.md`](CONTRIBUTING.md): contribution guidelines
- [`CHANGELOG.md`](CHANGELOG.md): change history

Documentation is under development. Contributions and suggestions are welcome in accordance with the repository guidelines.

## Academic Context

**Lambda Constellation** is being developed as an **undergraduate thesis (TCC)** on browser-based volunteer computing, combinatorial search-space exploration, and reusable platforms supported by coding agents.

The project investigates how to make distributed computing infrastructure more accessible to researchers experimenting with their own algorithms, while keeping scientific logic separate from application infrastructure.

## License

See [`LICENSE`](LICENSE) for the terms governing the use and distribution of this repository.
