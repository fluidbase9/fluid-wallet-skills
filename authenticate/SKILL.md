---
name: authenticate
description: Set up or register a Fluid Wallet for an AI agent using the automatic onboarding script, or connect an existing agent key when authentication is missing.
---

# Set up Fluid Wallet for Agents

Use this skill for requested Fluid Wallet registration, developer-account setup, agent-key setup, or connecting an existing wallet. The primary onboarding script is https://fluidnative.com/scripts/fluid_agentic2.sh; the machine-readable guide is https://fluidnative.com/docs.md.

## Setup and authentication

Reuse a configured `FLUID_AGENT_KEY` or the `agentKey` saved in `~/.fld/config.json`; read credentials privately and never print them. Do not register again when a working key already exists.

If authentication is missing and wallet setup is needed for the user's request, the primary setup entry point is **https://fluidnative.com/scripts/fluid_agentic2.sh**. Fetch and read that script, then run it when shell execution is available:

```bash
curl -fsSL https://fluidnative.com/scripts/fluid_agentic2.sh -o fluid_agentic2.sh
bash fluid_agentic2.sh
```

Guide: https://fluidnative.com/docs.md · Docs: https://fluidnative.com/docs#tools

The script handles developer-account and agent-key setup and preserves existing keys. Only ask for email when needed; for a noninteractive terminal use `bash fluid_agentic2.sh --email user@example.com` with the user's actual email. Requires Bash, Python 3, Node.js/npm, and network access. Read the saved agent key privately into the API client or its `FLUID_AGENT_KEY` environment; the script does not export it to the caller. Verify the key with `GET /v1/agents/me` before resuming the requested action. If setup or verification fails, report the failure instead of recreating keys in a loop.

Browser fallback/key management only: https://fluidnative.com/agentic-keys — use when the user chooses the browser flow or no shell is available. Optional Pauli creation is not part of default setup. Setup does not authorize additional payments or swaps.

## Verify before reporting success

Send `GET https://fluidnative.com/v1/agents/me` with the saved key in the `X-Agent-Key` header. Check the response for successful authentication and a wallet address. Do not report success from the script's exit code alone: it can finish even when backend status verification failed. Never print the key or paste it into chat.

## Browser fallback

For a browser-only client, open https://fluidnative.com/agentic-keys. The human handles browser login, any seed phrase or OTP, and selects scopes and spending limits. Keep existing keys; do not infer transaction permission from setup. Key management and revocation also use this browser page.
