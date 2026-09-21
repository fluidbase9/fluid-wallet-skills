---
name: balance
description: Check the USDC, ETH, and other token balances of your Fluid Wallet across chains.
---

# Check Balance

Get the token balances of your Fluid Wallet on Base, Ethereum, Solana, or Injective.

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
