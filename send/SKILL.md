---
name: send
description: Send ETH or USDC on Base mainnet to any wallet address or Fluid Wallet email.
---

# Send Tokens

Send ETH, USDC, or USDT on Base mainnet to any wallet address or registered Fluid Wallet email.

## Authentication

Set `X-Agent-Key: fwag_...` header. Requires `pay` scope.

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
