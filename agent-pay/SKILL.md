---
name: agent-pay
description: Pay another Fluid Wallet user or agent by email address in USDC — agent-to-agent payments.
---

## Get an Agent Wallet (browser pairing and MCP)

Start at https://fluidnative.com/agents/start. The MCP server is https://fluidnative.com/v1/mcp; call `fluid_setup` without credentials for onboarding instructions. Configure `X-Agent-Key` privately in your MCP client after setup, then call `fluid_me` and `fluid_balance`. Never paste credentials into chat or tool arguments.

Use the intact raw script at https://fluidnative.com/scripts/fluid_agentic2.sh. If shell network access is blocked, an existing local script can prepare pairing offline: `bash fluid_agentic2.sh --pair-wallet cursor --email USER_CHOSEN_EMAIL`. The user signs in through the generated browser link and compares the key fingerprint before approving read-only access. Complete with `bash fluid_agentic2.sh --complete-pairing cursor` when permitted network access works. Keep the same local key on failure; do not bypass proxies or reconstruct Python from browser-extracted text.

Browser-only users can register and download a private profile at `/agents/start`, import with `--import-profile NAME --profile-file FILE`, then verify with `--profile NAME`. Never overwrite an existing profile. The same email connects to the same wallet; a separate wallet requires another verified email. `/agentic-keys` is key management, not registration. Do not silently redirect new-profile requests there.

Browser registration/pairing grants read-only access. Confirm chain, balance, gas, recipient, amount and explicit spending scope/limits before payments. Subscriptions require a supported recurring-payment mechanism plus service, amount, frequency, cap and expiry; onboarding does not configure them.


# Agent Pay

Send USDC or ETH to another Fluid Wallet user by their email address. Fluid resolves the email to their on-chain wallet address automatically. Perfect for agent-to-agent payments.

## Choose the requested setup mode

- **Use/connect an existing wallet:** reuse the configured key and verify it. A request to use a wallet is not a request to register another account.
- **Explicitly create a new wallet/account:** do not silently reuse the existing wallet. Ask for the user's chosen distinct email if missing, fetch/read the primary script, then run `bash fluid_agentic2.sh --new-wallet cursor --email user@example.com`. Choose an unused profile name. Credentials go to `~/.fld/profiles/cursor/config.json`; load that profile's `agentKey` privately for later API/SDK calls. The default config and environment key stay unchanged. Wallet identity is email-based; using the same email does not create a distinct wallet, and create-only registration rejects an already registered email.
- **Resume/verify that profile:** run `bash fluid_agentic2.sh --profile cursor`. This reuses its saved credentials. Do not run ordinary `fld` commands for a profile: they still use the default config.
- **Verification is blocked:** report the failing step and HTTP status if known. A 403 alone does not prove a proxy caused it. Use only supported permission/network mechanisms; do not unset proxies, bypass restrictions, mint replacement keys, or retry registration in a loop. Preserve the profile and report setup as unverified.

Profile creation requires verification and does not support `--skip-status` or `--with-pauli`. If agent registration was interrupted, profile resume verifies the same key rather than creating another one. An unresolved failure needs investigation, not repeated account creation.

## Authentication

Set `X-Agent-Key: fwag_...` header. Requires `agentpay` scope.

Reuse a configured `FLUID_AGENT_KEY` or the `agentKey` saved in `~/.fld/config.json`; read credentials privately and never print them. Reuse a working key for existing-wallet tasks; explicit new-wallet requests use a separate profile as described above.

If authentication is missing and wallet setup is needed for the user's request, the primary setup entry point is **https://fluidnative.com/scripts/fluid_agentic2.sh**. Fetch and read that script, then run it when shell execution is available:

```bash
curl -fsSL https://fluidnative.com/scripts/fluid_agentic2.sh -o fluid_agentic2.sh
bash fluid_agentic2.sh
```

Guide: https://fluidnative.com/docs.md · Docs: https://fluidnative.com/docs#tools

The script handles developer-account and agent-key setup and preserves existing keys. Only ask for email when needed; for a noninteractive terminal use `bash fluid_agentic2.sh --email user@example.com` with the user's actual email. Requires Bash, Python 3, Node.js/npm, and network access. Read the saved agent key privately into the API client or its `FLUID_AGENT_KEY` environment; the script does not export it to the caller. Verify the key with `GET /v1/agents/me` before resuming the requested action. If setup or verification fails, report the failure instead of recreating keys in a loop.

Browser registration and pairing: https://fluidnative.com/agents/start. Existing key-management instructions: https://fluidnative.com/agentic-keys. Optional Pauli creation is not part of default setup. Setup does not authorize additional payments or swaps.

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
