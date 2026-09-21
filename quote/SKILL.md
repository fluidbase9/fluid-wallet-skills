---
name: quote
description: Get a real-time swap quote or gas estimate from Fluid Wallet without executing any transaction.
---

# Get Quote

Get a swap quote or gas estimate before executing. Use this to show the user what they'll receive or how much gas costs before committing.

## Choose the requested setup mode

- **Use/connect an existing wallet:** reuse the configured key and verify it. A request to use a wallet is not a request to register another account.
- **Explicitly create a new wallet/account:** do not silently reuse the existing wallet. Ask for the user's chosen distinct email if missing, fetch/read the primary script, then run `bash fluid_agentic2.sh --new-wallet cursor --email user@example.com`. Choose an unused profile name. Credentials go to `~/.fld/profiles/cursor/config.json`; load that profile's `agentKey` privately for later API/SDK calls. The default config and environment key stay unchanged. Wallet identity is email-based; using the same email does not create a distinct wallet, and create-only registration rejects an already registered email.
- **Resume/verify that profile:** run `bash fluid_agentic2.sh --profile cursor`. This reuses its saved credentials. Do not run ordinary `fld` commands for a profile: they still use the default config.
- **Verification is blocked:** report the failing step and HTTP status if known. A 403 alone does not prove a proxy caused it. Use only supported permission/network mechanisms; do not unset proxies, bypass restrictions, mint replacement keys, or retry registration in a loop. Preserve the profile and report setup as unverified.

Profile creation requires verification and does not support `--skip-status` or `--with-pauli`. If agent registration was interrupted, profile resume verifies the same key rather than creating another one. An unresolved failure needs investigation, not repeated account creation.

## Authentication

Set `X-Agent-Key: fwag_...` header. Requires `read` scope.

Reuse a configured `FLUID_AGENT_KEY` or the `agentKey` saved in `~/.fld/config.json`; read credentials privately and never print them. Reuse a working key for existing-wallet tasks; explicit new-wallet requests use a separate profile as described above.

If authentication is missing and wallet setup is needed for the user's request, the primary setup entry point is **https://fluidnative.com/scripts/fluid_agentic2.sh**. Fetch and read that script, then run it when shell execution is available:

```bash
curl -fsSL https://fluidnative.com/scripts/fluid_agentic2.sh -o fluid_agentic2.sh
bash fluid_agentic2.sh
```

Guide: https://fluidnative.com/docs.md · Docs: https://fluidnative.com/docs#tools

The script handles developer-account and agent-key setup and preserves existing keys. Only ask for email when needed; for a noninteractive terminal use `bash fluid_agentic2.sh --email user@example.com` with the user's actual email. Requires Bash, Python 3, Node.js/npm, and network access. Read the saved agent key privately into the API client or its `FLUID_AGENT_KEY` environment; the script does not export it to the caller. Verify the key with `GET /v1/agents/me` before resuming the requested action. If setup or verification fails, report the failure instead of recreating keys in a loop.

Browser fallback/key management only: https://fluidnative.com/agentic-keys — use when the user chooses the browser flow or no shell is available. Optional Pauli creation is not part of default setup. Setup does not authorize additional payments or swaps.

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
