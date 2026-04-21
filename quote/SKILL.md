---
name: quote
description: Get a real-time swap quote or gas estimate from Fluid Wallet without executing any transaction.
---

# Get Quote

Get a swap quote or gas estimate before executing. Use this to show the user what they'll receive or how much gas costs before committing.

## Authentication

Set `X-Agent-Key: fwag_...` header. Requires `read` scope.

## Endpoints

**Swap quote:**
```
POST https://fluidnative.com/v1/agents/quote-swap
```

**Gas estimate:**
```
POST https://fluidnative.com/v1/agents/estimate-gas
```

## Swap Quote Parameters

| Parameter | Required | Description |
|---|---|---|
| `fromToken` | yes | Source token: `ETH`, `USDC`, `USDT` |
| `toToken` | yes | Destination token |
| `amount` | yes | Amount as string |

## Gas Estimate Parameters

| Parameter | Required | Default | Description |
|---|---|---|---|
| `to` | yes | — | Recipient address |
| `amount` | yes | — | Amount to send |
| `token` | no | `ETH` | Token symbol |
| `chain` | no | `base` | Chain |

## Examples

**Swap quote:**
```json
POST /v1/agents/quote-swap
{ "fromToken": "ETH", "toToken": "USDC", "amount": "0.1" }
```
Response:
```json
{
  "fromToken": "ETH",
  "toToken": "USDC",
  "amountIn": "0.1",
  "amountOut": "342.50",
  "priceImpact": "0.02%",
  "route": "Fluid AMM",
  "validFor": "30s"
}
```

**Gas estimate:**
```json
POST /v1/agents/estimate-gas
{ "to": "0xRecipient...", "amount": "10", "token": "USDC", "chain": "base" }
```
Response:
```json
{
  "estimatedGasUsd": "0.001",
  "estimatedGasEth": "0.0000003",
  "chain": "base"
}
```

## SDK Usage

```typescript
import { FluidAgent } from 'fluid-wallet-agentkit';
const agent = new FluidAgent({ apiKey: process.env.FLUID_AGENT_KEY });

const quote = await agent.quoteSwap({ fromToken: 'ETH', toToken: 'USDC', amount: '0.1' });
console.log(`You'll receive ${quote.amountOut} USDC`);

const gas = await agent.estimateGas({ to: '0x...', amount: '10', token: 'USDC' });
console.log(`Gas cost: $${gas.estimatedGasUsd}`);
```
