---
name: send
description: Send ETH or USDC on Base mainnet to any wallet address or Fluid Wallet email.
---

# Send Tokens

Send ETH, USDC, or USDT on Base mainnet to any wallet address or registered Fluid Wallet email.

## Authentication

Set `X-Agent-Key: fwag_...` header. Requires `pay` scope.

Get a key: https://fluidnative.com/agentic-keys

## Endpoint

```
POST https://fluidnative.com/v1/agents/send
```

## Parameters

| Parameter | Required | Default | Description |
|---|---|---|---|
| `to` | yes | — | Wallet address (`0x...`) or Fluid email |
| `amount` | yes | — | Amount as string, e.g. `"0.01"` |
| `token` | no | `"ETH"` | `ETH`, `USDC`, `USDT` |
| `chain` | no | `"base"` | `base`, `ethereum` |

## Examples

**Send USDC to a wallet:**
```json
POST /v1/agents/send
{ "to": "0xRecipient...", "amount": "10", "token": "USDC", "chain": "base" }
```

**Send to another Fluid user by email:**
```json
POST /v1/agents/send
{ "to": "alice@example.com", "amount": "5", "token": "USDC" }
```

**Send ETH:**
```json
POST /v1/agents/send
{ "to": "0xRecipient...", "amount": "0.001", "token": "ETH", "chain": "base" }
```

## Response

```json
{
  "txHash": "0xabc123...",
  "from": "0xYourWallet...",
  "to": "0xRecipient...",
  "amount": "10",
  "token": "USDC",
  "chain": "base",
  "explorerUrl": "https://basescan.org/tx/0xabc123..."
}
```

## SDK Usage

```typescript
import { FluidAgent } from 'fluid-wallet-agentkit';
const agent = new FluidAgent({ apiKey: process.env.FLUID_AGENT_KEY });

const result = await agent.send({
  to: 'alice@example.com',
  amount: '10',
  token: 'USDC',
  chain: 'base',
});
console.log('TxHash:', result.txHash);
```

## Safety

- Transactions under the per-tx limit execute automatically
- Transactions over the limit send an approval email to the wallet owner
- Daily cap blocks all transactions if exceeded
