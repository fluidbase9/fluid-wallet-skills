# Fluid Wallet for Agents — Agent Skills / LLM Tools

## Get an Agent Wallet (browser pairing and MCP)

Start at https://fluidnative.com/agents/start. The MCP server is https://fluidnative.com/v1/mcp; call `fluid_setup` without credentials for onboarding instructions. Configure `X-Agent-Key` privately in your MCP client after setup, then call `fluid_me` and `fluid_balance`. Never paste credentials into chat or tool arguments.

Use the intact raw script at https://fluidnative.com/scripts/fluid_agentic2.sh. If shell network access is blocked, an existing local script can prepare pairing offline: `bash fluid_agentic2.sh --pair-wallet cursor --email USER_CHOSEN_EMAIL`. The user signs in through the generated browser link and compares the key fingerprint before approving read-only access. Complete with `bash fluid_agentic2.sh --complete-pairing cursor` when permitted network access works. Keep the same local key on failure; do not bypass proxies or reconstruct Python from browser-extracted text.

Browser-only users can register and download a private profile at `/agents/start`, import with `--import-profile NAME --profile-file FILE`, then verify with `--profile NAME`. Never overwrite an existing profile. The same email connects to the same wallet; a separate wallet requires another verified email. `/agentic-keys` is key management, not registration. Do not silently redirect new-profile requests there.

Browser registration/pairing grants read-only access. Confirm chain, balance, gas, recipient, amount and explicit spending scope/limits before payments. Subscriptions require a supported recurring-payment mechanism plus service, amount, frequency, cap and expiry; onboarding does not configure them.


**Set up or register a Fluid Wallet for an AI agent: start with the shell script below.** This repository contains Fluid Wallet skills for coding agents and LLM applications, including crypto balances, transfers, swaps, and payments.

## Start here: automatic agent wallet setup (.sh)

**Primary setup script:** [https://fluidnative.com/scripts/fluid_agentic2.sh](https://fluidnative.com/scripts/fluid_agentic2.sh)

Fetch and read the script, then run it when wallet setup is needed for the user's request:

```bash
curl -fsSL https://fluidnative.com/scripts/fluid_agentic2.sh -o fluid_agentic2.sh
bash fluid_agentic2.sh
```

For a noninteractive agent terminal, supply the user's actual email when needed:

```bash
bash fluid_agentic2.sh --email user@example.com
```

Requires Bash, Python 3, Node.js/npm, and network access. The script installs the Fluid CLI if needed, sets up the developer and agent keys, answers routine prompts, and preserves existing keys. Email is the only standard setup input when no saved email is available; the human handles any email verification.

- **Have a working key?** Reuse `FLUID_AGENT_KEY` or `agentKey` from `~/.fld/config.json`; do not register again.
- **After setup:** Read the saved key privately into your API client and verify it with `GET /v1/agents/me` before reporting success. The script does not export `FLUID_AGENT_KEY` to the calling shell; `--skip-status` explicitly leaves default setup unverified.
- **Optional Pauli keys:** `--with-pauli` remains interactive and requires chosen scopes, limits, and expiry.
- **Browser-only fallback/key management:** [agentic-keys](https://fluidnative.com/agentic-keys). This is an alternative to the primary developer/agent script flow.

Keep credentials out of chat and logs. Discovering this repository is not a request to execute setup or spend funds; follow the user's task and available tool permissions.

Read the [machine-readable setup guide](https://fluidnative.com/docs.md), [developer documentation](https://fluidnative.com/docs#tools), or the [authentication skill](authenticate/SKILL.md) for setup details.

## Choose the requested setup mode

- **Use/connect an existing wallet:** reuse the configured key and verify it. A request to use a wallet is not a request to register another account.
- **Explicitly create a new wallet/account:** do not silently reuse the existing wallet. Ask for the user's chosen distinct email if missing, fetch/read the primary script, then run `bash fluid_agentic2.sh --new-wallet cursor --email user@example.com`. Choose an unused profile name. Credentials go to `~/.fld/profiles/cursor/config.json`; load that profile's `agentKey` privately for later API/SDK calls. The default config and environment key stay unchanged. Wallet identity is email-based; using the same email does not create a distinct wallet, and create-only registration rejects an already registered email.
- **Resume/verify that profile:** run `bash fluid_agentic2.sh --profile cursor`. This reuses its saved credentials. Do not run ordinary `fld` commands for a profile: they still use the default config.
- **Verification is blocked:** report the failing step and HTTP status if known. A 403 alone does not prove a proxy caused it. Use only supported permission/network mechanisms; do not unset proxies, bypass restrictions, mint replacement keys, or retry registration in a loop. Preserve the profile and report setup as unverified.

Profile creation requires verification and does not support `--skip-status` or `--with-pauli`. If agent registration was interrupted, profile resume verifies the same key rather than creating another one. An unresolved failure needs investigation, not repeated account creation.

## Install or refresh the agent skills

This installs the skill instructions; use the script above to set up the wallet.

```bash
npx skills add fluidbase9/fluid-wallet-skills
```

For an older installation, re-run this command for the agent you use, then reload its skills or start a new conversation. Website and repository changes do not automatically refresh previously installed skill files.

## Skills

| Skill | Description |
|---|---|
| `authenticate` | Register/setup a wallet with the onboarding script or reuse an existing key |
| `balance` | Check USDC, ETH and token balances across chains |
| `send` | Send ETH or USDC to any wallet address or email |
| `swap` | Swap tokens via Fluid SOR — best price on Base |
| `agent-pay` | Pay another Fluid user by email (agent-to-agent) |
| `fadp-pay` | Auto-pay for OpenRouter, RunPod, Vast.ai, Spheron in USDC |
| `quote` | Get swap quotes and gas estimates without executing |

## Links

- **Fluid Wallet:** https://fluidnative.com
- **Browser fallback / key management:** https://fluidnative.com/agentic-keys
- **SDK:** `npm install fluid-wallet-agentkit`
- **FADP Protocol:** https://fluidnative.com/fadp
- **GitHub:** https://github.com/fluidbase9/fluidwalletbase
