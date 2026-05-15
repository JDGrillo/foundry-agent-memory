# Architecture Overview
## Version: 0.1.0

## 1. System Context Diagram

```mermaid
graph TB
    Dev[Developer / Demo Engineer]
    
    subgraph "foundry-agent-memory project"
        Scripts[Scenario Scripts]
        Utils[Memory Utilities]
        Config[Environment Config]
    end
    
    subgraph "Azure AI Foundry"
        AIP[AIProjectClient]
        subgraph "Memory Stores"
            MS1[helpdesk-memory<br/>Per-User Scope]
            MS2[standup-memory<br/>Group Scope]
            MS3a[cs-user-memory<br/>Per-User Scope]
            MS3b[cs-team-memory<br/>Group Scope]
        end
        subgraph "Model Deployments"
            Chat[gpt-4o / gpt-5.2]
            Embed[text-embedding-3-small]
        end
        subgraph "Agents (Portal-Visible)"
            A1[IT Help Desk Agent<br/>MemorySearchTool]
            A2[Standup Bot Agent<br/>MemorySearchTool]
            A3[Customer Success Agent<br/>2x MemorySearchTool]
        end
    end
    
    Dev --> Scripts
    Scripts --> Utils
    Utils --> AIP
    AIP --> MS1
    AIP --> MS2
    AIP --> MS3a
    AIP --> MS3b
    A1 -.->|MemorySearchTool| MS1
    A2 -.->|MemorySearchTool| MS2
    A3 -.->|MemorySearchTool| MS3a
    A3 -.->|MemorySearchTool| MS3b
    A1 --> Chat
    A2 --> Chat
    A3 --> Chat
    MS1 --> Embed
    MS2 --> Embed
    MS3a --> Embed
    MS3b --> Embed
```

## 2. Component Architecture

```mermaid
graph LR
    subgraph "src/"
        subgraph "scenarios/"
            S1[helpdesk.py]
            S2[standup.py]
            S3[customer_success.py]
        end
        subgraph "memory/"
            MU[store_manager.py]
            MC[config.py]
        end
        subgraph "agents/"
            AD[agent_definitions.py]
        end
        subgraph "common/"
            ENV[env.py]
            SIM[user_simulator.py]
        end
    end
    
    S1 --> MU
    S2 --> MU
    S3 --> MU
    S1 --> AD
    S2 --> AD
    S3 --> AD
    S1 --> SIM
    S2 --> SIM
    S3 --> SIM
    MU --> MC
    MC --> ENV
```

## 3. Data Flow

### Per-User Memory Flow (IT Help Desk)

```mermaid
sequenceDiagram
    participant U as User (simulated)
    participant S as Script
    participant A as Help Desk Agent
    participant M as Memory Store
    participant E as Embedding Model

    Note over S: Session 1 - First interaction
    U->>S: "My laptop keeps crashing"
    S->>A: Send message (scope=user_123)
    A->>M: MemorySearchTool query (scope=user_123)
    M-->>A: No prior memory
    A->>U: "What OS and model?"
    U->>S: "Windows 11, Surface Pro 9, 16GB"
    A->>M: Store user profile + issue context
    M->>E: Generate embedding
    A->>U: Resolution steps
    
    Note over S: Session 2 - Return visit
    U->>S: "Screen flickering again"
    S->>A: Send message (scope=user_123)
    A->>M: MemorySearchTool query (scope=user_123)
    M-->>A: "Surface Pro 9, Win11, prior crash issue"
    A->>U: "Since you're on your Surface Pro 9 with the prior crash history, let's check if the display driver update from last time held..."
```

### Group Memory Flow (Standup Bot)

```mermaid
sequenceDiagram
    participant U1 as Alice
    participant U2 as Bob
    participant S as Script
    participant A as Standup Agent
    participant M as Memory Store (team scope)

    Note over S: Monday standup
    U1->>A: "Working on auth refactor, blocked by API team"
    A->>M: Store (scope=team-alpha, user=alice)
    U2->>A: "Finishing DB migration, on track"
    A->>M: Store (scope=team-alpha, user=bob)
    
    Note over S: Tuesday standup
    U1->>A: "Auth refactor still blocked"
    A->>M: Query team-alpha context
    M-->>A: "Alice blocked since Monday on auth refactor (API team dependency)"
    A->>U1: "This is day 2 blocked on API team. Should we escalate?"
    U2->>A: "DB migration complete, picking up frontend"
    A->>M: Update bob's status
    A-->>S: Summary: "Recurring blocker: Alice/auth/API team (2 days)"
```

## 4. Data Model

| Entity | Key Fields | Relationships |
|--------|-----------|---------------|
| Memory Store | `id`, `name`, `embedding_model_deployment`, `chat_summary_enabled`, `user_profile_enabled`, `user_profile_details` | Has many Scopes |
| Scope | `scope_id` (e.g., `user_123`, `team-alpha`) | Belongs to Memory Store; contains memory entries |
| Memory Entry | `content`, `embedding`, `timestamp`, `metadata` | Belongs to Scope |
| Agent | `id`, `name`, `instructions`, `tools[]` | Created via Foundry Agents Service; visible in portal; references Memory Store via `MemorySearchTool` |

### Memory Store Configurations

| Scenario | Store Name | Scope Strategy | chat_summary | user_profile | user_profile_details |
|----------|-----------|---------------|--------------|--------------|---------------------|
| IT Help Desk | `helpdesk-memory` | `{{$userId}}` (per-user) | `true` | `true` | "role, department, OS, device model, RAM, software versions, past issues and resolutions, communication preference" |
| Standup Bot | `standup-memory` | Static team ID (e.g., `team-alpha`) | `true` | `false` | N/A |
| Customer Success (user) | `cs-user-memory` | `{{$userId}}` (per-CSM) | `true` | `true` | "interaction style, client portfolio, specialization areas" |
| Customer Success (team) | `cs-team-memory` | Static account ID (e.g., `account-acme`) | `true` | `false` | N/A |

## 5. Technology Stack

| Layer | Technology | Rationale |
|-------|-----------|-----------|
| Language | Python 3.11+ | Foundry SDKs are Python-first |
| Foundry Agents SDK | `azure-ai-agents` | Agent CRUD, MemorySearchTool, threads/runs — portal-visible agents |
| Azure SDK | `azure-ai-projects`, `azure-identity` | Memory Store CRUD (`client.beta.memory_stores`), auth |
| Chat Model | `gpt-4o` or `gpt-5.2` | Agent reasoning |
| Embedding Model | `text-embedding-3-small` | Memory store embeddings |
| Config | python-dotenv + `.env` | Simple local config |
| Package Manager | pip + `requirements.txt` | Simplicity for demo |

## 6. Key Design Decisions

| ID | Decision | Rationale | Status |
|----|----------|-----------|--------|
| ADD-001 | CLI scripts per scenario, not a unified app | Each scenario is self-contained and independently runnable for demos | Accepted |
| ADD-002 | Simulated users via script (not real auth) | Demo simplicity; real auth would require AAD app registration setup | Accepted |
| ADD-003 | Shared `store_manager.py` for memory CRUD | DRY; all scenarios need create/configure/teardown | Accepted |
| ADD-004 | Use `{{$userId}}` scope for per-user, static strings for group | Matches documented Foundry memory scoping patterns | Accepted |
| ADD-005 | Single `requirements.txt`, no poetry/pdm | Lowest barrier to entry for demo consumers | Accepted |
| ADD-006 | Include cleanup/teardown utility | Prevent orphaned memory stores in dev subscriptions | Accepted |
| ADD-007 | Use Foundry Agents Service API for agent creation (not only local Agent Framework) | Agents must be visible in the Azure AI Foundry portal with MemorySearchTool attached; portal visibility is a demo requirement (REQ-F-034). Agents created via `project_client.agents.create_agent()` are registered server-side and inspectable in the portal. Conversations run via the threads/runs API. | Accepted |
| ADD-008 | Memory data must be inspectable post-demo | Demo engineers need to show stored memory (profiles, summaries, scoped entries) in the Foundry portal or via a utility script (REQ-F-036) | Accepted |

## 7. Security Architecture

- **Authentication**: `DefaultAzureCredential` — supports local dev (Azure CLI login) and managed identity
- **Secrets**: All connection strings and endpoints in `.env` (gitignored); `.env.example` committed with placeholders
- **No PII in source**: Simulated user data only; no real user information in scripts
- **Memory Store access**: Controlled by Azure RBAC on the AI Foundry project
- **Scope isolation**: Per-user scopes ensure one user's memory is not accessible to queries scoped to another user

## 8. Observability Architecture

- **Logging**: Python `logging` module at INFO level; DEBUG available via env var
- **Console output**: Rich formatted output showing memory retrieval vs. fresh response (makes memory value visible)
- **No external telemetry**: Demo project; no Application Insights or distributed tracing needed

## 9. Infrastructure Requirements (Non-Implementation)

| Resource | Configuration | Notes |
|----------|--------------|-------|
| Azure AI Foundry Project | Standard tier | Must support Memory Stores |
| Chat model deployment | `gpt-4o` or `gpt-5.2`, standard throughput | Agent reasoning |
| Embedding model deployment | `text-embedding-3-small` | Memory embeddings |
| Azure subscription | Contributor or AI Developer role | For resource provisioning |
