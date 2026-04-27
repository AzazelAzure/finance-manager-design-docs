# Future Risks & Mitigation Strategy

## Technical Risks

### 1. Zero-Knowledge Transition
- **Issue**: Converting a live database with unencrypted user data to a ZK model is a high-risk operation that could lead to data loss or "permanent lockouts" if keys are lost.
- **Mitigation**: Start tagging models now with `is_encrypted` flags. Develop robust migration and key-recovery workflows during Phase 2.

### 2. Multi-Agent Concurrency
- **Issue**: As we use more subagents, the risk of file conflicts (two agents editing `shell.py` simultaneously) increases.
- **Mitigation**: Enforce "Component Ownership." Agents should be assigned to specific features (e.g., "Agent-Profile") rather than broad files.

### 3. Mobile UI Complexity
- **Issue**: A "Widget-Style" dashboard that looks great on a 4K monitor may become unusable on a smartphone.
- **Mitigation**: Implement a "Responsive Grid" early in the widget architecture. Plan for a "Mobile Dashboard" view that stacks widgets vertically or uses a simplified KPI list.

## Business Risks

### 1. Data Privacy Compliance (GDPR/CCPA)
- **Issue**: Handling real financial data for global users requires strict legal compliance.
- **Mitigation**: Maintain the "Account Deletion = Full Wipe" standard established in `PLAN_API_Account_Deletion`. Ensure logging NEVER touches PII.

### 2. Feature Creep
- **Issue**: Adding Budgeting, Savings, and Credit Cards simultaneously could dilute the quality of the core Transactions engine.
- **Mitigation**: Follow the "API-First" golden rule. Do not implement a UI feature until the backend math is 100% verified via CLI.
