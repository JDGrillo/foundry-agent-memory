# PRD: foundry-agent-memory
## Version: 0.1.0
## Date: 2025-05-15
## Status: In Review

## 1. Executive Summary

A demonstration project showcasing Microsoft Foundry Agents' long-term memory capabilities through practical, real-world scenarios. The project implements three distinct use cases — per-user memory, group/shared memory, and hybrid memory — using the Foundry Agents Service (`azure-ai-agents` SDK) and Foundry Memory Stores.

The goal is to provide developers with concrete, non-contrived examples of how agent memory creates tangible value: faster support interactions, persistent team knowledge, and seamless handoffs.

## 2. Business Context & Goals

| Goal | Metric |
|------|--------|
| Demonstrate per-user memory value | Returning user gets contextual response without re-explaining setup |
| Demonstrate group memory value | Any team member can access shared knowledge accumulated over time |
| Demonstrate hybrid memory | Seamless handoff between team members with no context loss |
| Serve as reference implementation | Developers can fork and adapt scenarios to their own domains |
| Showcase Memory Store API surface | CRUD operations, scoping, search, profile building all exercised |

## 3. Target Users / Personas

| Persona | Description | Goal |
|---------|-------------|------|
| **Platform Developer** | Builds AI agents on Foundry | Understand how to wire memory stores into agents |
| **Solution Architect** | Designs multi-agent systems | See memory partitioning patterns (per-user, group, hybrid) |
| **Demo Engineer** | Runs live demos of Foundry capabilities | Execute scenarios end-to-end with visible memory effects |

## 4. Success Metrics

- All three scenarios run end-to-end with a single `python run_scenario.py` per scenario
- Memory persistence is observable: second interaction demonstrably leverages first interaction's context
- Group memory is accessible across simulated users
- Setup from zero to running demo < 15 minutes (with pre-provisioned Azure resources)
- Agents and memory stores are visible in the Azure AI Foundry portal after scenario execution
- Memory data (profiles, summaries) is inspectable in the portal or via utility script post-demo

## 5. Scope

### 5.1 In Scope
- Three runnable scenario scripts (IT Help Desk, Standup Bot, Customer Success)
- Agents created via Foundry Agents Service (portal-visible with MemorySearchTool)
- Shared utility module for memory store provisioning and configuration
- Environment configuration template (`.env.example`)
- README with setup instructions and scenario walkthroughs
- Memory data inspection utility for post-demo verification
- Python project using `azure-ai-agents` (Foundry Agents Service) and `azure-ai-projects` (Memory Store CRUD)

### 5.2 Out of Scope
- Production deployment (no CI/CD, no containerization)
- Web UI or chat interface (CLI/script interactions only)
- Authentication beyond DefaultAzureCredential
- Load testing or performance benchmarking
- Multi-region or high-availability configurations

### 5.3 Future Considerations
- Streamlit or Chainlit UI for interactive demos
- Additional scenarios (e.g., onboarding agent, sales copilot)
- Memory analytics/visualization
- Memory retention policies and TTL management

## 6. Dependencies & Integrations

| Dependency | Type | Notes |
|-----------|------|-------|
| `azure-ai-agents` | Python package | Foundry Agents Service — agent CRUD, MemorySearchTool, threads/runs |
| `azure-ai-projects` | Python package | AIProjectClient for memory store CRUD |
| `azure-identity` | Python package | DefaultAzureCredential |
| `python-dotenv` | Python package | `.env` file loading |
| Azure AI Foundry project | Cloud resource | Must be pre-provisioned |
| Embedding model deployment | Cloud resource | `text-embedding-3-small` (or equivalent) |
| Chat model deployment | Cloud resource | `gpt-4o` or `gpt-5.2` |
| Foundry Memory Stores (preview) | Platform feature | Must be enabled on the project |

## 7. Open Questions

| ID | Question | Status |
|----|----------|--------|
| OQ-1 | Is there a minimum SDK version required for Memory Store APIs? | Open |
| OQ-2 | Should scenarios simulate multi-turn within a single script run, or support resumable sessions? | Open — assuming single script with multi-turn simulation |
| OQ-3 | Are there quota limits on memory stores per project that affect demo setup? | Open |
| OQ-4 | Should the project include teardown/cleanup scripts for memory stores? | Resolved — yes, `cleanup.py` and `inspect_memory.py` planned (REQ-F-032, REQ-F-036) |
