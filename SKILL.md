---
name: qq-onebot-openclaw-recovery
description: Restore and verify the existing Windows QQ route from NapCatQQ through OneBot into OpenClaw without switching to a different QQ integration. Use when a user wants the current QQ bridge recovered, NapCat ports or token aligned with OpenClaw, QQNT metadata or file-integrity issues diagnosed, or a minimal whitelist-safe end-to-end QQ acceptance run on the local machine.
---

# QQ OneBot OpenClaw Recovery

## Overview

Use this skill to execute the fixed Windows recovery path for QQ control of OpenClaw:
read the execution doc if one is provided, do only the minimum preflight checks,
repair the bridge first, probe OpenClaw second, and finish with the smallest
possible whitelist-safe message verification.

Default route:
`NapCatQQ -> OneBot -> openclaw-onebot -> OpenClaw`

Do not pivot to a different QQ integration unless the user explicitly changes scope.

## Guardrails

- Keep everything local-only. Do not expose NapCat or OpenClaw to the public internet.
- Prefer the currently installed and enabled NapCat route over fresh installs or new architectures.
- Do not widen allowlists, add more groups, or broaden test coverage unless the user explicitly asks.
- Do not rewrite the task into a general QQ integration comparison.
- Do not use self-send acceptance as final inbound proof. `message_sent` or `message_sent/self` is not external inbound evidence.
- If any published artifact would contain a real token, QQ number, hostname, or personal path, redact it before saving or pushing.

## Required Order

1. Read the execution doc and extract only the concrete constraints.
2. Perform minimum preflight checks:
   confirm NapCat is the bridge, confirm actual local ports, confirm token alignment.
3. Repair bridge-side issues first.
4. Run OpenClaw channel probe second.
5. Run minimal outbound and inbound message verification third.
6. Report exactly what was executed, what passed, whether QQ can currently operate OpenClaw, and what risks remain.

## Windows Reference

If you are working on Windows, read [references/windows-onebot-checklist.md](references/windows-onebot-checklist.md) for typical local paths, command snippets, and log signatures.

## Workflow

### 1. Read The Source Of Truth

- Start from the user-supplied execution doc if one exists.
- Extract the route, ordering constraints, whitelist boundaries, and rollback expectations.
- Treat those constraints as binding unless the user explicitly changes them.

### 2. Run Minimum Preflight Checks

- Confirm the active bridge is NapCatQQ.
- Confirm QQ is installed and logged in.
- Read OpenClaw OneBot config from the local OpenClaw config file.
- Read NapCat OneBot config and compare actual WebSocket port, HTTP port, and access token.
- Confirm listeners remain on loopback only.

If the user asked for minimal verification, stop here and do not perform broader architecture analysis.

### 3. Repair The Bridge First

- Fix the smallest bridge-side mismatch before touching OpenClaw runtime behavior.
- Preserve a rollback path when editing local config or version metadata.
- A common failure is NapCat QQNT metadata lagging behind the installed QQNT build, which can surface as QQ file-integrity or file-corruption complaints. Align NapCat's local QQNT metadata to the installed QQNT build before changing OpenClaw.
- After each bridge change, verify the local listener and the NapCat status API before moving on.

### 4. Probe OpenClaw Second

- Run `openclaw channels status --probe`.
- Success target: the gateway is reachable and the OneBot channel is connected.
- If probe still fails because the bridge is disconnected, return to bridge repair instead of widening scope into unrelated OpenClaw changes.

### 5. Run Minimal Message Verification

- First verify outbound send from OpenClaw to one approved allowlisted private target or one approved allowlisted group.
- Then verify a real inbound message from an external allowlisted source.
- Final inbound proof requires both:
  - NapCat logging a real incoming event that converts to OneBot `post_type:"message"`
  - OpenClaw logging receipt of that incoming message and producing a downstream response or action
- Reject self-send acceptance cases where the event is only `message_sent` or `message_sent/self`.

### 6. Close Out With An Accurate Status

Always return:

- the exact steps executed
- the verification result for each step
- whether QQ can currently operate OpenClaw
- unresolved risks and follow-up suggestions
- any files changed, backups created, and remaining manual actions

If outbound is working but no external inbound proof exists yet, say so plainly instead of claiming success.

## Decision Rules

- If NapCatQQ is already the installed bridge, stay on that route.
- If NapCat and OpenClaw disagree on ports or token, prefer aligning NapCat to the current OpenClaw configuration unless the user explicitly asks to move OpenClaw instead.
- If a decision would widen exposure, broaden the whitelist, or switch architectures, stop and ask first.
- If a message-validation branch depends on a live external sender, request the smallest possible follow-up message from one approved allowlisted source and then immediately inspect NapCat and OpenClaw logs.

## Example Triggers

- "Recover QQ -> OneBot -> OpenClaw on the current route only"
- "Check whether the bridge is NapCatQQ and align local ports and token"
- "QQ files keep failing integrity checks, diagnose NapCat and OpenClaw"
- "Repair the bridge first, then probe, then run minimal QQ message acceptance"
