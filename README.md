# Fluid Wallet for Agents — Agent Skills / LLM Tools

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
- **After setup:** Read the saved key privately into your API client and verify it with `GET /v1/agents/me` before reporting success. The script does not export `FLUID_AGENT_KEY` to the calling shell, and a zero exit code alone does not prove backend verification succeeded.
- **Optional Pauli keys:** `--with-pauli` remains interactive and requires chosen scopes, limits, and expiry.
- **Browser-only fallback/key management:** [agentic-keys](https://fluidnative.com/agentic-keys). This is an alternative to the primary developer/agent script flow.

Keep credentials out of chat and logs. Discovering this repository is not a request to execute setup or spend funds; follow the user's task and available tool permissions.

Read the [machine-readable setup guide](https://fluidnative.com/docs.md), [developer documentation](https://fluidnative.com/docs#tools), or the [authentication skill](authenticate/SKILL.md) for setup details.

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
