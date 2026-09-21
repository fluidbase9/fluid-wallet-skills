---
name: balance
description: Check the USDC, ETH, and other token balances of your Fluid Wallet across chains.
---

# Check Balance

Get the token balances of your Fluid Wallet on Base, Ethereum, Solana, or Injective.

## Authentication

Set `X-Agent-Key: fwag_...` header. Requires `read` scope.

Reuse a configured `FLUID_AGENT_KEY` or the `agentKey` saved in `~/.fld/config.json`; read credentials privately and never print them. Do not register again when a working key already exists.

If authentication is missing and wallet setup is needed for the user's request, the primary setup entry point is **https://fluidnative.com/scripts/fluid_agentic2.sh**. Fetch and read that script, then run it when shell execution is available:

```bash
curl -fsSL https://fluidnative.com/scripts/fluid_agentic2.sh -o fluid_agentic2.sh
bash fluid_agentic2.sh
```

Guide: https://fluidnative.com/docs.md · Docs: https://fluidnative.com/docs#tools

The script handles developer-account and agent-key setup and preserves existing keys. Only ask for email when needed; for a noninteractive terminal use `bash fluid_agentic2.sh --email user@example.com` with the user's actual email. Requires Bash, Python 3, Node.js/npm, and network access. Read the saved agent key privately into the API client or its `FLUID_AGENT_KEY` environment; the script does not export it to the caller. Verify the key with `GET /v1/agents/me` before resuming the requested action. If setup or verification fails, report the failure instead of recreating keys in a loop.

Browser fallback/key management only: https://fluidnative.com/agentic-keys — use when the user chooses the browser flow or no shell is available. Optional Pauli creation is not part of default setup. Setup does not authorize additional payments or swaps.

## Endpoint

```
GET https://fluidnative.com/v1/agents/balance
GET https://fluidnative.com/v1/agents/balance?chain=base
```

## Parameters

| Parameter | Required | Default | Description |
|---|---|---|---|
| `chain` | no | all chains | `base`, `ethereum`, `solana`, `injective` |

## Response

```json
{
  "walletAddress": "0xYour...",
  "balances": [
    { "chain": "base", "token": "USDC", "balance": "10.500000", "balanceUsd": "10.50" },
    { "chain": "base", "token": "ETH",  "balance": "0.00312",   "balanceUsd": "10.67" }
  ],
  "ethPriceUsd": 3420.00
}
```

## SDK Usage

```typescript
import { FluidAgent } from 'fluid-wallet-agentkit';
const agent = new FluidAgent({ apiKey: process.env.FLUID_AGENT_KEY });

const balance = await agent.getBalance('base');
console.log(balance);
// { token: 'USDC', balance: '10.50', balanceUsd: '10.50', chain: 'base' }
```

## Notes

- Always check balance before sending to ensure sufficient funds
- USDC on Base: contract `0x833589fCD6eDb6E08f4c7C32D4f71b54bdA02913`
- Balances are live from Base RPC — not cached
