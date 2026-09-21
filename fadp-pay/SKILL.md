---
name: fadp-pay
description: Automatically pay for any FADP-gated service (OpenRouter, RunPod, Vast.ai, Spheron) in USDC using Fluid Wallet.
---

## Get an Agent Wallet (browser pairing and MCP)

Start at https://fluidnative.com/agents/start. The MCP server is https://fluidnative.com/v1/mcp; call `fluid_setup` without credentials for onboarding instructions. Configure `X-Agent-Key` privately in your MCP client after setup, then call `fluid_me` and `fluid_balance`. Never paste credentials into chat or tool arguments.

Use the intact raw script at https://fluidnative.com/scripts/fluid_agentic2.sh. If shell network access is blocked, an existing local script can prepare pairing offline: `bash fluid_agentic2.sh --pair-wallet cursor --email USER_CHOSEN_EMAIL`. The user signs in through the generated browser link and compares the key fingerprint before approving read-only access. Complete with `bash fluid_agentic2.sh --complete-pairing cursor` when permitted network access works. Keep the same local key on failure; do not bypass proxies or reconstruct Python from browser-extracted text.

Browser-only users can register and download a private profile at `/agents/start`, import with `--import-profile NAME --profile-file FILE`, then verify with `--profile NAME`. Never overwrite an existing profile. The same email connects to the same wallet; a separate wallet requires another verified email. `/agentic-keys` is key management, not registration. Do not silently redirect new-profile requests there.

Browser registration/pairing grants read-only access. Confirm chain, balance, gas, recipient, amount and explicit spending scope/limits before payments. Subscriptions require a supported recurring-payment mechanism plus service, amount, frequency, cap and expiry; onboarding does not configure them.


# FADP Auto-Pay

Pay for AI services (LLM calls, GPU jobs, compute) automatically in USDC using the Fluid Agentic DeFi Protocol (FADP). No credit card needed — the agent pays from your Fluid Wallet.

## Choose the requested setup mode

- **Use/connect an existing wallet:** reuse the configured key and verify it. A request to use a wallet is not a request to register another account.
- **Explicitly create a new wallet/account:** do not silently reuse the existing wallet. Ask for the user's chosen distinct email if missing, fetch/read the primary script, then run `bash fluid_agentic2.sh --new-wallet cursor --email user@example.com`. Choose an unused profile name. Credentials go to `~/.fld/profiles/cursor/config.json`; load that profile's `agentKey` privately for later API/SDK calls. The default config and environment key stay unchanged. Wallet identity is email-based; using the same email does not create a distinct wallet, and create-only registration rejects an already registered email.
- **Resume/verify that profile:** run `bash fluid_agentic2.sh --profile cursor`. This reuses its saved credentials. Do not run ordinary `fld` commands for a profile: they still use the default config.
- **Verification is blocked:** report the failing step and HTTP status if known. A 403 alone does not prove a proxy caused it. Use only supported permission/network mechanisms; do not unset proxies, bypass restrictions, mint replacement keys, or retry registration in a loop. Preserve the profile and report setup as unverified.

Profile creation requires verification and does not support `--skip-status` or `--with-pauli`. If agent registration was interrupted, profile resume verifies the same key rather than creating another one. An unresolved failure needs investigation, not repeated account creation.

## What is FADP?

FADP is an HTTP 402-based payment protocol. When you call a FADP-gated service:
1. Server returns `402 Payment Required` + `X-FADP-Required` header
2. Your agent pays the required USDC on Base automatically
3. Agent retries the request with `X-FADP-Proof` header
4. Server verifies payment on-chain and returns the response

## Authentication

Set `FLUID_AGENT_KEY=fwag_...` in your environment. Requires `pay` scope.

Reuse a configured `FLUID_AGENT_KEY` or the `agentKey` saved in `~/.fld/config.json`; read credentials privately and never print them. Reuse a working key for existing-wallet tasks; explicit new-wallet requests use a separate profile as described above.

If authentication is missing and wallet setup is needed for the user's request, the primary setup entry point is **https://fluidnative.com/scripts/fluid_agentic2.sh**. Fetch and read that script, then run it when shell execution is available:

```bash
curl -fsSL https://fluidnative.com/scripts/fluid_agentic2.sh -o fluid_agentic2.sh
bash fluid_agentic2.sh
```

Guide: https://fluidnative.com/docs.md · Docs: https://fluidnative.com/docs#tools

The script handles developer-account and agent-key setup and preserves existing keys. Only ask for email when needed; for a noninteractive terminal use `bash fluid_agentic2.sh --email user@example.com` with the user's actual email. Requires Bash, Python 3, Node.js/npm, and network access. Read the saved agent key privately into the API client or its `FLUID_AGENT_KEY` environment; the script does not export it to the caller. Verify the key with `GET /v1/agents/me` before resuming the requested action. If setup or verification fails, report the failure instead of recreating keys in a loop.

Browser registration and pairing: https://fluidnative.com/agents/start. Existing key-management instructions: https://fluidnative.com/agentic-keys. Optional Pauli creation is not part of default setup. Setup does not authorize additional payments or swaps.

## Setup

```bash
npm install fluid-wallet-agentkit
```

Enable FADP auto-pay in your agent:
```typescript
import { FluidAgent } from 'fluid-wallet-agentkit';

const agent = new FluidAgent({
  apiKey: process.env.FLUID_AGENT_KEY,
  fadp: true,        // enable auto-pay
  fadpMaxUsd: 1,     // safety limit per call
});
```

## Run a FADP Proxy Locally

```bash
npm install -g fluid-fadp-proxy

FLUID_WALLET_ADDRESS=0xYour... \
OPENROUTER_API_KEY=sk-or-... \
npx fluid-fadp-proxy
# Listening on http://localhost:4000
```

## Call Paid Services (auto-pays USDC)

**OpenRouter / GPT-4o — 0.001 USDC per call:**
```typescript
const res = await agent.fetch('http://localhost:4000/openrouter/chat/completions', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({
    model: 'openai/gpt-4o',
    messages: [{ role: 'user', content: 'Summarize today\'s DeFi news' }],
  }),
});
const { choices } = await res.json();
console.log(choices[0].message.content);
```

**RunPod GPU job — 0.05 USDC per job:**
```typescript
const job = await agent.fetch('http://localhost:4000/runpod/YOUR_ENDPOINT/run', {
  method: 'POST',
  body: JSON.stringify({ input: { prompt: 'Generate an image of...' } }),
});
```

**Vast.ai GPU instance — 0.10 USDC:**
```typescript
const instance = await agent.fetch('http://localhost:4000/vastai/asks/12345', {
  method: 'PUT',
  body: JSON.stringify({ price: 0.5, disk: 10 }),
});
```

**Spheron deployment — 0.02 USDC:**
```typescript
const deploy = await agent.fetch('http://localhost:4000/spheron/compute/instance', {
  method: 'POST',
  body: JSON.stringify({ name: 'my-app', image: 'nginx:latest' }),
});
```

## Pricing

| Service | Endpoint | Price |
|---|---|---|
| OpenRouter (LLM calls) | `/openrouter/chat/completions` | 0.001 USDC |
| RunPod (GPU jobs) | `/runpod/:id/run` | 0.05 USDC |
| Vast.ai (GPU rental) | `/vastai/asks/:id` | 0.10 USDC |
| Spheron (deployments) | `/spheron/compute/instance` | 0.02 USDC |

## Protocol Docs

- FADP spec: https://fluidnative.com/fadp
- npm: `fluid-fadp` (server middleware), `fluid-fadp-proxy` (proxy)
- GitHub: https://github.com/fluidbase9/fadp
