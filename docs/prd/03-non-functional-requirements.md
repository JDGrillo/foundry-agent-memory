# Non-Functional Requirements
## Version: 0.1.0

### REQ-NF-001: Setup Time
- **Category**: Accessibility
- **Priority**: P0
- **Status**: Draft
- **Description**: A developer with pre-provisioned Azure resources should go from clone to running demo in under 15 minutes
- **Acceptance Criteria**:
  - [ ] AC-1: README provides step-by-step instructions that require no external lookups
  - [ ] AC-2: `pip install -r requirements.txt` installs all dependencies
  - [ ] AC-3: Single `.env` file is the only configuration needed

### REQ-NF-002: Script Independence
- **Category**: Reliability
- **Priority**: P0
- **Status**: Draft
- **Description**: Each scenario script is independently runnable without requiring other scenarios to have run first
- **Acceptance Criteria**:
  - [ ] AC-1: `python -m scenarios.helpdesk` runs without running standup or customer_success first
  - [ ] AC-2: Each scenario provisions its own memory stores if they don't exist
- **Dependencies**: [REQ-F-030]

### REQ-NF-003: Idempotent Setup
- **Category**: Reliability
- **Priority**: P0
- **Status**: Draft
- **Description**: Running setup/provisioning multiple times produces no errors or side effects
- **Acceptance Criteria**:
  - [ ] AC-1: Memory store creation is idempotent (creates if missing, returns if exists)
  - [ ] AC-2: No duplicate stores or scopes from repeated runs

### REQ-NF-004: Clear Error Messages
- **Category**: Accessibility
- **Priority**: P1
- **Status**: Draft
- **Description**: All failure modes produce actionable error messages
- **Acceptance Criteria**:
  - [ ] AC-1: Missing env vars produce message naming the variable and where to set it
  - [ ] AC-2: Auth failures suggest `az login` or credential setup
  - [ ] AC-3: Missing model deployments name the required deployment and how to create it

### REQ-NF-005: Python Version Compatibility
- **Category**: Accessibility
- **Priority**: P1
- **Status**: Draft
- **Description**: Project runs on Python 3.11+
- **Acceptance Criteria**:
  - [ ] AC-1: No syntax or features requiring Python 3.12+ unless noted
  - [ ] AC-2: `requirements.txt` specifies compatible version ranges

### REQ-NF-006: No Sensitive Data in Source
- **Category**: Security
- **Priority**: P0
- **Status**: Draft
- **Description**: No real credentials, connection strings, or PII in committed files
- **Acceptance Criteria**:
  - [ ] AC-1: `.env` is in `.gitignore`
  - [ ] AC-2: `.env.example` contains only placeholders
  - [ ] AC-3: Simulated user data uses obviously fake names/details
- **Dependencies**: [REQ-F-031]

### REQ-NF-007: Demo Readability
- **Category**: Accessibility
- **Priority**: P1
- **Status**: Draft
- **Description**: Console output is formatted for live demo presentation
- **Acceptance Criteria**:
  - [ ] AC-1: Clear visual separation between interactions/turns
  - [ ] AC-2: Color or formatting distinguishes user input, agent response, and memory events
  - [ ] AC-3: Output is readable at standard terminal width (120 chars)
- **Dependencies**: [REQ-F-033]
