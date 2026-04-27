# Hive Mission Protocol (v1.0)

The Hive Mission Protocol defines the standard for stateless, atomic task execution within the Finance Manager project.

## 1. Roles

- **Orchestrator (Antigravity/Gemini Pro)**: The "Parent" agent. Responsible for high-level reasoning, plan creation, mission delegation, and quality control (Review).
- **Worker (Hive-CLI/Gemini Flash)**: The "Child" agent. Responsible for executing a single Task ID in isolation.

## 2. Plan Structure (Mandatory)

For the `hive_worker.py` to parse tasks correctly, they must follow this structure in the `PLAN_*.md`:

```markdown
### Task T<ID>: <Short Name>
- **Goal:** Clear description of what to achieve.
- **Files to edit:**
    - `path/to/file1.py`
    - `path/to/file2.py`
- **Implementation notes:** Specific technical constraints or logic.
- **Verification commands:** (Optional) Command to run after edit.
```

## 3. Workflow

1.  **Plan Generation**: Orchestrator creates a detailed plan.
2.  **Delegation**: Orchestrator runs `scripts/hive_worker.py --plan <PLAN> --task <ID>`.
3.  **Execution**: 
    - Worker creates a `hive/T<ID>-<name>` branch.
    - Worker performs the edit and runs verification.
4.  **Review**: Orchestrator (Antigravity) reviews the branch/diff.
5.  **Merge/Archival**: If approved, Orchestrator merges the branch and marks the task as `[x]` in the plan.

## 4. Guiding Principles

- **Statelessness**: Every worker task starts from a clean branch.
- **Atomicity**: One task should ideally modify fewer than 3 files and be under 200 lines of change.
- **Verification First**: Tasks should include a verification command whenever possible.
