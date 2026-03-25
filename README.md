# qq-onebot-openclaw-recovery

A Codex skill for restoring and verifying the existing Windows QQ route:

`NapCatQQ -> OneBot -> openclaw-onebot -> OpenClaw`

This skill is intentionally narrow. It keeps the current route, repairs the bridge first, probes OpenClaw second, and finishes with the smallest possible local-only verification.

## What It Covers

- Confirm NapCatQQ is the active bridge.
- Align local ports and access token with OpenClaw.
- Diagnose QQNT metadata drift that can surface as QQ file-integrity failures.
- Run a minimal OneBot and OpenClaw acceptance check without widening exposure.

## Guardrails

- Local-only, no public ingress.
- No route switching unless the user explicitly changes scope.
- No widened allowlists or broader group coverage by default.
- No self-send event accepted as final inbound proof.

## Files

- `SKILL.md`: main workflow and decision rules
- `agents/openai.yaml`: skill metadata for Codex
- `references/windows-onebot-checklist.md`: Windows paths, commands, and log signatures

## Install

Copy this directory into your Codex skills directory as:

`~/.codex/skills/qq-onebot-openclaw-recovery`

## Use

Trigger it with a prompt such as:

`Use $qq-onebot-openclaw-recovery to restore and verify the fixed NapCatQQ through OneBot into OpenClaw route on this machine.`
