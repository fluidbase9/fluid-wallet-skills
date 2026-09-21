---
name: fadp-pay
description: Automatically pay for any FADP-gated service (OpenRouter, RunPod, Vast.ai, Spheron) in USDC using Fluid Wallet.
---

# FADP Auto-Pay

Pay for AI services (LLM calls, GPU jobs, compute) automatically in USDC using the Fluid Agentic DeFi Protocol (FADP). No credit card needed — the agent pays from your Fluid Wallet.

## What is FADP?

FADP is an HTTP 402-based payment protocol. When you call a FADP-gated service:
1. Server returns `402 Payment Required` + `X-FADP-Required` header
2. Your agent pays the required USDC on Base automatically
3. Agent retries the request with `X-FADP-Proof` header
4. Server verifies payment on-chain and returns the response

## Authentication

Set `FLUID_AGENT_KEY=fwag_...` in your environment. Requires `pay` scope.

Reuse a configured `FLUID_AGENT_KEY` or the `agentKey` saved in `~/.fld/config.json`; read credentials privately and never print them. Do not register again when a working key already exists.

If authentication is missing and wallet setup is needed for the user's request, the primary setup entry point is **https://fluidnative.com/scripts/fluid_agentic2.sh**. Fetch and read that script, then run it when shell execution is available:

```bash
curl -fsSL https://fluidnative.com/scripts/fluid_agentic2.sh -o fluid_agentic2.sh
bash fluid_agentic2.sh
```

Guide: https://fluidnative.com/docs.md · Docs: https://fluidnative.com/docs#tools

The script handles developer-account and agent-key setup and preserves existing keys. Only ask for email when needed; for a noninteractive terminal use `bash fluid_agentic2.sh --email user@example.com` with the user's actual email. Requires Bash, Python 3, Node.js/npm, and network access. Read the saved agent key privately into the API client or its `FLUID_AGENT_KEY` environment; the script does not export it to the caller. Verify the key with `GET /v1/agents/me` before resuming the requested action. If setup or verification fails, report the failure instead of recreating keys in a loop.

Browser fallback/key management only: https://fluidnative.com/agentic-keys — use when the user chooses the browser flow or no shell is available. Optional Pauli creation is not part of default setup. Setup does not authorize additional payments or swaps.

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
