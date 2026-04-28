---
name: balance
description: Check the USDC, ETH, and other token balances of your Fluid Wallet across chains.
---

# Check Balance

Get the token balances of your Fluid Wallet on Base, Ethereum, Solana, or Injective.

## Authentication

Set `X-Agent-Key: fwag_...` header. Requires `read` scope.

Get a key: https://fluidnative.com/agentic-keys

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
