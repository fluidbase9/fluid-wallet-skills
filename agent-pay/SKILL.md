---
name: agent-pay
description: Pay another Fluid Wallet user or agent by email address in USDC — agent-to-agent payments.
---

# Agent Pay

Send USDC or ETH to another Fluid Wallet user by their email address. Fluid resolves the email to their on-chain wallet address automatically. Perfect for agent-to-agent payments.

## Authentication

Set `X-Agent-Key: fwag_...` header. Requires `agentpay` scope.

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
