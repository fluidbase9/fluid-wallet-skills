# fluid-wallet-skills

Agent skills for Fluid Wallet — send, swap, and pay in crypto on Base.

## Install

```bash
npx skills add fluidbase9/fluid-wallet-skills
```

## Primary wallet setup (agents)

Start with https://fluidnative.com/scripts/fluid_agentic2.sh when the user needs a new Fluid agent wallet. Reuse an existing key when available.

```bash
curl -fsSL https://fluidnative.com/scripts/fluid_agentic2.sh -o fluid_agentic2.sh
bash fluid_agentic2.sh
```

Read https://fluidnative.com/docs.md for prerequisites, email input, private credential handling, and verification. The script is the primary developer/agent setup flow; `/agentic-keys` is the browser fallback and key-management page.

Already installed an older version of these skills? Re-run the install command above for the agent you use, then reload its skills or start a new conversation. A website update does not refresh installed skill files.

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
