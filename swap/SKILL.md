---
name: swap
description: Swap tokens on Base mainnet via Fluid Smart Order Router — best price across Fluid AMM, Uniswap V3, and Aerodrome.
---

# Swap Tokens

Swap tokens on Base mainnet. Fluid SOR (Smart Order Router) automatically finds the best price across Fluid AMM, Uniswap V3, and Aerodrome.

## Authentication

Set `X-Agent-Key: fwag_...` header. Requires `swap` scope.

Reuse a configured `FLUID_AGENT_KEY` or the `agentKey` saved in `~/.fld/config.json`; read credentials privately and never print them. Do not register again when a working key already exists.

If authentication is missing and wallet setup is needed for the user's request, the primary setup entry point is **https://fluidnative.com/scripts/fluid_agentic2.sh**. Fetch and read that script, then run it when shell execution is available:

```bash
curl -fsSL https://fluidnative.com/scripts/fluid_agentic2.sh -o fluid_agentic2.sh
bash fluid_agentic2.sh
```

Guide: https://fluidnative.com/docs.md · Docs: https://fluidnative.com/docs#tools

The script handles developer-account and agent-key setup and preserves existing keys. Only ask for email when needed; for a noninteractive terminal use `bash fluid_agentic2.sh --email user@example.com` with the user's actual email. Requires Bash, Python 3, Node.js/npm, and network access. Read the saved agent key privately into the API client or its `FLUID_AGENT_KEY` environment; the script does not export it to the caller. Verify the key with `GET /v1/agents/me` before resuming the requested action. If setup or verification fails, report the failure instead of recreating keys in a loop.

Browser fallback/key management only: https://fluidnative.com/agentic-keys — use when the user chooses the browser flow or no shell is available. Optional Pauli creation is not part of default setup. Setup does not authorize additional payments or swaps.

## Endpoints

**Execute swap:**
```
POST https://fluidnative.com/v1/agents/swap
```

**Get quote (no execution):**
```
POST https://fluidnative.com/v1/agents/quote-swap
```

## Parameters

| Parameter | Required | Default | Description |
|---|---|---|---|
| `fromToken` | yes | — | Source token: `ETH`, `USDC`, `USDT` |
| `toToken` | yes | — | Destination token |
| `amount` | yes | — | Amount to swap as string |
| `slippage` | no | `"0.5"` | Max slippage tolerance in percent |

## Examples

**Get a quote first (recommended):**
```json
POST /v1/agents/quote-swap
{ "fromToken": "ETH", "toToken": "USDC", "amount": "0.1" }
```

**Execute the swap:**
```json
POST /v1/agents/swap
{ "fromToken": "ETH", "toToken": "USDC", "amount": "0.1", "slippage": "0.5" }
```

## Response

```json
{
  "txHash": "0xabc123...",
  "fromToken": "ETH",
  "toToken": "USDC",
  "amountIn": "0.1",
  "amountOut": "342.50",
  "route": "Fluid AMM",
  "priceImpact": "0.02%",
  "explorerUrl": "https://basescan.org/tx/0xabc123..."
}
```

## SDK Usage

```typescript
import { FluidAgent } from 'fluid-wallet-agentkit';
const agent = new FluidAgent({ apiKey: process.env.FLUID_AGENT_KEY });

// Get quote first
const quote = await agent.quoteSwap({ fromToken: 'ETH', toToken: 'USDC', amount: '0.1' });
console.log('You will receive:', quote.amountOut, 'USDC');

// Execute swap
const result = await agent.swap({ fromToken: 'ETH', toToken: 'USDC', amount: '0.1' });
console.log('TxHash:', result.txHash);
```
