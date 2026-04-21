---
name: swap
description: Swap tokens on Base mainnet via Fluid Smart Order Router — best price across Fluid AMM, Uniswap V3, and Aerodrome.
---

# Swap Tokens

Swap tokens on Base mainnet. Fluid SOR (Smart Order Router) automatically finds the best price across Fluid AMM, Uniswap V3, and Aerodrome.

## Authentication

Set `X-Agent-Key: fwag_...` header. Requires `swap` scope.

Get a key: https://fluidnative.com/agentic-keys

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
