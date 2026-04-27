# Reflex Hybrid Interaction Governance

This document defines how to choose between native Reflex components, targeted custom bridges, and broader frontend migration work.

## Decision Principle

During prebeta and MVP hardening, Reflex remains the primary frontend runtime. Custom interaction requirements should be solved with the smallest safe architectural move that preserves delivery velocity.

## Decision Matrix

### 1) Use Native Reflex

Choose native Reflex when:
- The interaction can be implemented with existing Reflex and Radix/Recharts wrappers.
- Behavior is state-driven and does not require low-level DOM or chart internals.
- The result is maintainable without introducing custom JS build/runtime coupling.

Typical examples:
- forms, filters, toggles, table interactions, route-level shell behavior
- standard chart configuration (color, labels, tooltips, axes)

### 2) Use a Targeted Bridge Component

Use a targeted bridge when:
- A specific interaction is blocked by wrapper limitations (for example, active-shape pie lift).
- The requirement is isolated to one feature/widget and does not justify app-wide migration.
- A bounded component contract can be defined and tested.

Bridge constraints:
- Keep the bridge small, feature-local, and documented.
- Define explicit props/events and fallback behavior.
- Avoid introducing framework-level assumptions beyond the targeted interaction.

### 3) Escalate to Broader JS Migration Planning

Open a broader migration plan when at least two of these are true for 2-3 consecutive feature cycles:
- More than two critical UX requirements are blocked by Reflex wrapper limitations.
- Bridge components become maintenance hotspots (frequent regressions or upgrade breakage).
- Debug/runtime velocity is materially worse than direct JS implementations for core features.

## Prebeta Operational Rules

- Keep architecture changes scoped by subproject and by feature.
- Prefer incremental, reversible steps over all-at-once rewrites.
- Preserve API-first contract discipline and validate frontend behavior against live runtime workflows.

## Evidence Log For Re-evaluation

When deciding whether to stay hybrid or migrate, track:
- blocked interaction count per cycle
- bridge defect count and fix effort
- release impact from wrapper/bridge regressions
- implementation lead time for complex UI tasks

This evidence should drive migration planning rather than one-off frustration during active feature delivery.
