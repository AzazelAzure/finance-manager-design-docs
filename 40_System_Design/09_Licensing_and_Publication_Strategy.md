# Licensing and Publication Strategy (Draft v1)

## Purpose

Define a durable licensing/publication model that supports:
- proprietary full-product delivery (API + Reflex),
- open and reusable security middleware,
- a semi-public security testing sandbox for bounty workflows.

This document is a planning draft and not legal advice.

## Product Surface Model

## Surface A: Core Product (Private/Proprietary)
- Repos: full API, full Reflex frontend, internal ops/deployment tooling.
- Visibility: private.
- License model: proprietary internal/commercial terms.
- Reason: protect integrated product value, private workflows, and commercial differentiation.

## Surface B: Security Middleware (Open Source Target)
- Repo: Rust middleware and related integration libraries.
- Visibility: public.
- License model: to be selected at phase gate (permissive vs copyleft trade-off).
- Reason: industry trust, external review, broad adoption, ecosystem utility.

## Surface C: Bounty Sandbox Stack (Semi-Public)
- Repos: reduced/skeleton API + pseudo frontend + sandbox deployment assets.
- Visibility: public/semi-public by release.
- License model: source-available non-commercial terms (draft direction).
- Reason: enable adversarial testing without exposing full commercial implementation.

## Current-State Notes

- API currently contains AGPL licensing history from prior public state.
- API is now private; future re-publication/relicensing decisions remain open.
- Final publication boundaries must be locked before broad external contributions.

## Licensing Decision Gates

## Gate L1: Pre-Middleware Public Planning
- Confirm what code is permanently private.
- Confirm what middleware components are intended for public release.
- Confirm whether sandbox code is open-source or source-available only.

## Gate L2: Pre-First Public Middleware Release
- Select middleware license.
- Define contributor model (`CLA` or `DCO`) before accepting external contributions.
- Publish `SECURITY.md`, disclosure expectations, and support boundaries.

## Gate L3: Pre-Sandbox Release
- Publish non-commercial and acceptable-use terms for bounty sandbox.
- Confirm no production/private feature leakage in sandbox code/data.
- Confirm legal review for disclosure workflow and bounty language.

## Guardrails

- No production data in any public or semi-public testing surface.
- No direct dependency from public sandbox to private production secrets/config.
- No external contribution intake until contribution policy is explicitly published.
- Keep release notes explicit about what is and is not supported.

## Deferred Items

- Final license selection for middleware.
- Exact legal text for non-commercial sandbox use.
- Contributor ownership and relicensing process details.
