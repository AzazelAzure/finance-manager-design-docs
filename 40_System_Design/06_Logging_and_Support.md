# Server Logging & Support Strategy

## Overview
As the project moves from local development to a hosted Beta, logging must transition from "developer output" to "operational intelligence."

## Multi-Agent Logging Requirements
- **Per-User Context**: Loguru must be configured to tag every log entry with the `uid` of the authenticated user.
- **Critical Intercepts**: Any `CRITICAL` or `ERROR` level log should be captured and categorized for proactive bug fixing.
- **Data Privacy**: Logs must NEVER contain sensitive financial data or PII (Personally Identifiable Information). Only metadata and operation IDs are permitted.

## Support & Bug Reporting
- **Implementation Goal**: Build a "Bug Report" widget directly into the Reflex frontend.
- **Email Integration**: Integrated support system to notify the admin when a critical bug is reported.
- **Traceability**: Every bug report should automatically include the relevant `uid` and recent log sequence (metadata only) to streamline debugging.

## Long-Term Goals (Post-Beta)
- **Centralized Log Aggregation**: Moving from file-based logs to a centralized collector (e.g., ELK stack or a lightweight equivalent).
- **Automated Alerting**: Slack/Email alerts for system-wide failures.
