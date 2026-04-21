---
name: agent-pay
description: Pay another Fluid Wallet user or agent by email address in USDC — agent-to-agent payments.
---

# Agent Pay

Send USDC or ETH to another Fluid Wallet user by their email address. Fluid resolves the email to their on-chain wallet address automatically. Perfect for agent-to-agent payments.

## Authentication

Set `X-Agent-Key: fwag_...` header. Requires `agentpay` scope.

Get a key: https://fluidnative.com/agentic-keys

## Endpoint

```
POST https://fluidnative.com/v1/agents/agent-pay
```

## Parameters

| Parameter | Required | Default | Description |
|---|---|---|---|
| `toEmail` | yes | — | Recipient's Fluid Wallet email |
| `amount` | yes | — | Amount as string, e.g. `"5"` |
| `token` | no | `"USDC"` | `USDC`, `ETH` |
| `memo` | no | — | Optional payment note |

## Examples

**Pay another agent for a service:**
```json
POST /v1/agents/agent-pay
{
  "toEmail": "other-agent@example.com",
  "amount": "1",
  "token": "USDC",
  "memo": "Payment for data analysis"
}
```

## Response

```json
{
  "txHash": "0xabc123...",
  "from": "0xYourWallet...",
  "to": "0xRecipientWallet...",
  "toEmail": "other-agent@example.com",
  "amount": "1",
  "token": "USDC",
  "memo": "Payment for data analysis",
  "explorerUrl": "https://basescan.org/tx/0xabc123..."
}
```

## SDK Usage

```typescript
import { FluidAgent } from 'fluid-wallet-agentkit';
const agent = new FluidAgent({ apiKey: process.env.FLUID_AGENT_KEY });

const result = await agent.agentPay({
  toEmail: 'research-agent@example.com',
  amount: '1',
  token: 'USDC',
  memo: 'Market research report',
});
console.log('Paid:', result.txHash);
```

## Use Case: Agent Economy

Agents can pay each other for services:
- Agent A hires Agent B to research DeFi news → pays 1 USDC
- Agent B delivers the report → Agent A confirms receipt
- All payments are on-chain, verifiable on basescan.org
