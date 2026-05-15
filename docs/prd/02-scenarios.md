# Functional Requirements — Scenarios
## Version: 0.1.0

---

## Scenario 1: IT Help Desk Agent (Per-User Memory)

### REQ-F-001: Memory Store Provisioning — Help Desk
- **Priority**: P0
- **Status**: Draft
- **Description**: Create a memory store configured for per-user IT support context
- **Acceptance Criteria**:
  - [ ] AC-1: Memory store `helpdesk-memory` created via `AIProjectClient`
  - [ ] AC-2: `chat_summary_enabled = true`
  - [ ] AC-3: `user_profile_enabled = true`
  - [ ] AC-4: `user_profile_details` includes: role, department, OS, device model, RAM, software versions, past issues, resolutions, communication preference
  - [ ] AC-5: Scope set to `{{$userId}}` for automatic per-user partitioning
  - [ ] AC-6: Embedding model deployment (`text-embedding-3-small`) correctly referenced
- **User Story**: As a developer, I want a pre-configured memory store so that the help desk agent retains user-specific context.
- **Dependencies**: []
- **Notes**: Store creation is idempotent — re-running setup does not duplicate stores.

### REQ-F-002: Help Desk Agent Definition
- **Priority**: P0
- **Status**: Draft
- **Description**: Define an agent with IT support instructions and `MemorySearchTool` attached
- **Acceptance Criteria**:
  - [ ] AC-1: Agent created with system instructions for IT support behavior
  - [ ] AC-2: `MemorySearchTool` attached referencing `helpdesk-memory` store
  - [ ] AC-3: Agent uses chat model deployment (`gpt-4o` or `gpt-5.2`)
  - [ ] AC-4: Agent instructions direct it to leverage recalled memory context naturally
- **User Story**: As a developer, I want an agent definition that demonstrates memory-augmented IT support.
- **Dependencies**: [REQ-F-001]

### REQ-F-003: First-Contact Interaction (No Memory)
- **Priority**: P0
- **Status**: Draft
- **Description**: Simulate a new user's first interaction where no memory exists
- **Acceptance Criteria**:
  - [ ] AC-1: Script sends messages as a user with no prior memory
  - [ ] AC-2: Agent asks clarifying questions (device, OS, role) since no profile exists
  - [ ] AC-3: Conversation resolves an IT issue (e.g., laptop crashing)
  - [ ] AC-4: After conversation, memory store contains user profile data and issue summary
  - [ ] AC-5: Console output clearly indicates "No prior memory found for this user"
- **User Story**: As a demo viewer, I want to see the agent gathering context from scratch so I understand the baseline experience.
- **Dependencies**: [REQ-F-001, REQ-F-002]
- **Error Handling**:
  - If memory store is unreachable, agent falls back to stateless behavior with a warning

### REQ-F-004: Return-Visit Interaction (Memory Recall)
- **Priority**: P0
- **Status**: Draft
- **Description**: Simulate the same user returning with a new (related) issue; agent recalls prior context
- **Acceptance Criteria**:
  - [ ] AC-1: Script sends messages as the same user ID from REQ-F-003
  - [ ] AC-2: Agent retrieves stored profile (device, OS, prior issue) via MemorySearchTool
  - [ ] AC-3: Agent references prior context naturally in its response (e.g., "Since you're on your Surface Pro 9...")
  - [ ] AC-4: Agent does NOT re-ask questions already answered in the first session
  - [ ] AC-5: Console output shows memory retrieval with retrieved context visible
  - [ ] AC-6: New interaction data is appended to existing memory
- **User Story**: As a demo viewer, I want to see the tangible time savings when the agent remembers me.
- **Dependencies**: [REQ-F-003]

### REQ-F-005: Profile Evolution
- **Priority**: P1
- **Status**: Draft
- **Description**: Demonstrate that the user profile updates over time as new information emerges
- **Acceptance Criteria**:
  - [ ] AC-1: User mentions a new software tool or role change in conversation
  - [ ] AC-2: Subsequent memory retrieval reflects the updated profile
  - [ ] AC-3: Old information is not lost — profile accumulates
- **User Story**: As a developer, I want to see that memory is living/evolving, not static.
- **Dependencies**: [REQ-F-004]

---

## Scenario 2: Project Standup Bot (Group Memory)

### REQ-F-010: Memory Store Provisioning — Standup
- **Priority**: P0
- **Status**: Draft
- **Description**: Create a memory store configured for team-shared standup context
- **Acceptance Criteria**:
  - [ ] AC-1: Memory store `standup-memory` created via `AIProjectClient`
  - [ ] AC-2: `chat_summary_enabled = true`
  - [ ] AC-3: `user_profile_enabled = false` (group context, not individual profiles)
  - [ ] AC-4: Scope set to a static team identifier (e.g., `team-alpha`)
  - [ ] AC-5: All team members' updates stored under the same scope
- **User Story**: As a developer, I want a shared memory store that accumulates team standup context.
- **Dependencies**: []

### REQ-F-011: Standup Agent Definition
- **Priority**: P0
- **Status**: Draft
- **Description**: Define an agent that facilitates standups and maintains team awareness
- **Acceptance Criteria**:
  - [ ] AC-1: Agent created with standup facilitator instructions
  - [ ] AC-2: `MemorySearchTool` attached referencing `standup-memory` store
  - [ ] AC-3: Agent instructions direct it to track status, blockers, and progress
  - [ ] AC-4: Agent proactively surfaces patterns (recurring blockers, stalled work)
- **User Story**: As a developer, I want an agent that demonstrates group memory for team coordination.
- **Dependencies**: [REQ-F-010]

### REQ-F-012: Day 1 Standup (Initial Context)
- **Priority**: P0
- **Status**: Draft
- **Description**: Simulate a team's first standup with multiple team members reporting status
- **Acceptance Criteria**:
  - [ ] AC-1: Script simulates 2-3 team members providing standup updates
  - [ ] AC-2: Each update includes: what they're working on, any blockers, planned next steps
  - [ ] AC-3: Agent acknowledges and summarizes the standup
  - [ ] AC-4: Memory store contains all updates under the team scope
  - [ ] AC-5: Console output shows team members and their updates clearly
- **User Story**: As a demo viewer, I want to see a realistic standup flow captured in memory.
- **Dependencies**: [REQ-F-010, REQ-F-011]
- **Error Handling**:
  - If a team member's update is unclear, agent asks for clarification

### REQ-F-013: Day 2 Standup (Contextual Follow-up)
- **Priority**: P0
- **Status**: Draft
- **Description**: Simulate the next day's standup where the agent uses prior context
- **Acceptance Criteria**:
  - [ ] AC-1: Agent recalls yesterday's blockers and asks for updates on them specifically
  - [ ] AC-2: Agent notices if a blocker persists and suggests escalation
  - [ ] AC-3: Agent tracks progress (e.g., "Bob finished DB migration yesterday, now on frontend")
  - [ ] AC-4: New standup data is appended to existing team memory
  - [ ] AC-5: Console output shows retrieved team context before new standup begins
- **User Story**: As a demo viewer, I want to see the agent proactively using team history.
- **Dependencies**: [REQ-F-012]

### REQ-F-014: Standup Summary Generation
- **Priority**: P1
- **Status**: Draft
- **Description**: Agent produces a structured summary highlighting patterns across standups
- **Acceptance Criteria**:
  - [ ] AC-1: After Day 2 standup, agent generates a summary noting: recurring blockers, completed items, items in progress > N days
  - [ ] AC-2: Summary is output to console in a readable format
- **User Story**: As a demo viewer, I want to see the intelligence value of accumulated group memory.
- **Dependencies**: [REQ-F-013]

---

## Scenario 3: Customer Success Agent (Hybrid Per-User + Group)

### REQ-F-020: Memory Store Provisioning — Customer Success (Per-User)
- **Priority**: P0
- **Status**: Draft
- **Description**: Create a per-CSM memory store for individual work context
- **Acceptance Criteria**:
  - [ ] AC-1: Memory store `cs-user-memory` created via `AIProjectClient`
  - [ ] AC-2: `chat_summary_enabled = true`
  - [ ] AC-3: `user_profile_enabled = true`
  - [ ] AC-4: `user_profile_details` includes: interaction style, client portfolio, specialization areas
  - [ ] AC-5: Scope set to `{{$userId}}` for per-CSM partitioning
- **User Story**: As a developer, I want per-CSM memory so the agent adapts to each team member.
- **Dependencies**: []

### REQ-F-021: Memory Store Provisioning — Customer Success (Team/Account)
- **Priority**: P0
- **Status**: Draft
- **Description**: Create a shared memory store for account-level intelligence
- **Acceptance Criteria**:
  - [ ] AC-1: Memory store `cs-team-memory` created via `AIProjectClient`
  - [ ] AC-2: `chat_summary_enabled = true`
  - [ ] AC-3: `user_profile_enabled = false`
  - [ ] AC-4: Scope set to static account identifier (e.g., `account-acme`)
  - [ ] AC-5: All CSMs contributing to the same account share this scope
- **User Story**: As a developer, I want shared account memory so team knowledge is preserved.
- **Dependencies**: []

### REQ-F-022: Customer Success Agent Definition
- **Priority**: P0
- **Status**: Draft
- **Description**: Define an agent with dual MemorySearchTool references (per-user + group)
- **Acceptance Criteria**:
  - [ ] AC-1: Agent created with customer success instructions
  - [ ] AC-2: Two `MemorySearchTool` instances attached: one for `cs-user-memory`, one for `cs-team-memory`
  - [ ] AC-3: Agent instructions differentiate when to use personal vs. account memory
  - [ ] AC-4: Agent can synthesize information from both memory sources
- **User Story**: As a developer, I want to see how an agent combines multiple memory stores.
- **Dependencies**: [REQ-F-020, REQ-F-021]

### REQ-F-023: CSM Interaction — Building Account Knowledge
- **Priority**: P0
- **Status**: Draft
- **Description**: Simulate a CSM logging client interaction details that populate both memory stores
- **Acceptance Criteria**:
  - [ ] AC-1: CSM-1 (e.g., "Sarah") interacts with agent about client "Acme Corp"
  - [ ] AC-2: Personal memory captures Sarah's preferences and working style
  - [ ] AC-3: Account memory captures Acme Corp details (contract, preferences, issues)
  - [ ] AC-4: Console shows which memory store each piece of info is routed to
- **User Story**: As a demo viewer, I want to see dual-memory population in real time.
- **Dependencies**: [REQ-F-022]

### REQ-F-024: Seamless Handoff Between CSMs
- **Priority**: P0
- **Status**: Draft
- **Description**: A different CSM picks up the Acme Corp account and gets full context from shared memory
- **Acceptance Criteria**:
  - [ ] AC-1: CSM-2 (e.g., "Mike") asks agent about Acme Corp status
  - [ ] AC-2: Agent retrieves account context from `cs-team-memory` (populated by Sarah)
  - [ ] AC-3: Agent adapts to Mike's personal style from `cs-user-memory`
  - [ ] AC-4: Mike receives comprehensive account context without Sarah's involvement
  - [ ] AC-5: Console clearly shows "Retrieved from account memory (contributed by Sarah)"
- **User Story**: As a demo viewer, I want to see zero-friction handoff enabled by shared memory.
- **Dependencies**: [REQ-F-023]
- **Error Handling**:
  - If account memory is empty, agent prompts CSM to provide background

### REQ-F-025: Account Intelligence Accumulation
- **Priority**: P1
- **Status**: Draft
- **Description**: Multiple interactions build a rich account picture over time
- **Acceptance Criteria**:
  - [ ] AC-1: After 2+ interactions from different CSMs, account memory contains synthesized intelligence
  - [ ] AC-2: Agent can answer "What's the history with Acme Corp?" with a comprehensive response
  - [ ] AC-3: Escalation history and resolution patterns are surfaced
- **User Story**: As a demo viewer, I want to see institutional knowledge growing.
- **Dependencies**: [REQ-F-024]

---

## Cross-Scenario Requirements

### REQ-F-030: Shared Memory Store Manager
- **Priority**: P0
- **Status**: Draft
- **Description**: Utility module for memory store CRUD operations shared across all scenarios
- **Acceptance Criteria**:
  - [ ] AC-1: `create_store(name, config)` — creates or returns existing store
  - [ ] AC-2: `delete_store(name)` — removes store and all scopes
  - [ ] AC-3: `delete_scope(store_name, scope_id)` — removes specific scope data
  - [ ] AC-4: `list_stores()` — lists all memory stores in the project
  - [ ] AC-5: Idempotent creation (running twice doesn't error or duplicate)
- **User Story**: As a developer, I want reusable memory management so scenarios don't duplicate boilerplate.
- **Dependencies**: []

### REQ-F-031: Environment Configuration
- **Priority**: P0
- **Status**: Draft
- **Description**: `.env.example` with all required configuration and clear documentation
- **Acceptance Criteria**:
  - [ ] AC-1: `.env.example` contains all required variables with descriptive comments
  - [ ] AC-2: Variables include: `FOUNDRY_PROJECT_ENDPOINT`, `CHAT_MODEL_DEPLOYMENT`, `EMBEDDING_MODEL_DEPLOYMENT`
  - [ ] AC-3: `env.py` utility validates all required variables are set at startup
  - [ ] AC-4: Clear error messages when configuration is missing
- **User Story**: As a developer, I want clear configuration so I can get running quickly.
- **Dependencies**: []

### REQ-F-032: Cleanup/Teardown Utility
- **Priority**: P1
- **Status**: Draft
- **Description**: Script to clean up all memory stores created by the demo
- **Acceptance Criteria**:
  - [ ] AC-1: `cleanup.py` removes all memory stores created by the project
  - [ ] AC-2: Confirmation prompt before deletion
  - [ ] AC-3: Selective cleanup (by scenario) supported
- **User Story**: As a developer, I want easy cleanup so demo resources don't linger.
- **Dependencies**: [REQ-F-030]

### REQ-F-033: Console Output — Memory Visibility
- **Priority**: P1
- **Status**: Draft
- **Description**: All scenarios clearly show when memory is being stored vs. retrieved
- **Acceptance Criteria**:
  - [ ] AC-1: Memory retrieval events are visually distinct in console output
  - [ ] AC-2: Retrieved memory content is displayed (truncated if long)
  - [ ] AC-3: "No memory found" is explicitly shown on first interactions
  - [ ] AC-4: Memory write events are confirmed in output
- **User Story**: As a demo viewer, I want to SEE the memory working, not just infer it from responses.
- **Dependencies**: []

### REQ-F-034: Portal Visibility — Agents
- **Priority**: P0
- **Status**: Draft
- **Description**: Agents created by each scenario must be visible in the Azure AI Foundry portal under the project's Agents blade
- **Acceptance Criteria**:
  - [ ] AC-1: Agents are created via the Foundry Agents Service API (`project_client.agents.create_agent()`) so they are registered server-side
  - [ ] AC-2: Each agent is visible in the Foundry portal with its name, instructions, and attached tools
  - [ ] AC-3: `MemorySearchTool` is visible as an attached tool on each agent in the portal, referencing the correct memory store
  - [ ] AC-4: Agent conversations (threads) are visible in the portal
  - [ ] AC-5: Console output includes agent ID and a portal URL hint after creation
- **User Story**: As a demo engineer, I want to open the Foundry portal and see the agent I just created, with memory enabled, so I can show this in a live demo.
- **Dependencies**: [REQ-F-030]
- **Notes**: This requires using the Foundry Agents Service (`azure-ai-agents` SDK) rather than purely local Agent Framework agents. Agents created via `agent_framework.Agent` with `FoundryMemoryProvider` run locally and are NOT visible in the portal.

### REQ-F-035: Portal Visibility — Memory Stores
- **Priority**: P0
- **Status**: Draft
- **Description**: Memory stores created by each scenario must be visible and inspectable in the Azure AI Foundry portal
- **Acceptance Criteria**:
  - [ ] AC-1: Memory stores appear in the Foundry portal under the project's memory/storage section
  - [ ] AC-2: Store configuration is visible in the portal (chat_summary_enabled, user_profile_enabled, user_profile_details)
  - [ ] AC-3: Embedding model and chat model associations are visible
  - [ ] AC-4: Console output confirms store creation with name and ID
- **User Story**: As a demo engineer, I want to show the memory store configuration in the portal to explain how memory is set up.
- **Dependencies**: [REQ-F-030]
- **Notes**: Memory stores created via `client.beta.memory_stores.create()` are already portal-visible. This requirement ensures this is exercised and demonstrated.

### REQ-F-036: Portal Visibility — Memory Data Inspection
- **Priority**: P1
- **Status**: Draft
- **Description**: After running a scenario, the stored memory data (user profiles, chat summaries, scoped entries) should be viewable in the Foundry portal or via a script
- **Acceptance Criteria**:
  - [ ] AC-1: After a scenario runs, memory entries are visible in the Foundry portal under the memory store
  - [ ] AC-2: Scopes are visible (e.g., `user_123`, `team-alpha`, `account-acme`)
  - [ ] AC-3: User profile data (where enabled) is viewable in the portal
  - [ ] AC-4: If portal inspection is limited, provide a `inspect_memory.py` utility that queries and displays memory contents via the SDK
- **User Story**: As a demo engineer, I want to inspect the actual stored memory data after running a scenario, either in the portal or via a script, to prove memory persistence is working.
- **Dependencies**: [REQ-F-034, REQ-F-035]
