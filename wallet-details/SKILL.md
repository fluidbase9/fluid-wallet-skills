---
name: wallet-details
description: Query a Fluid agent wallet's identity, blockchain network, chain ID, token metadata, permissions and current Base balances using its existing authenticated profile.
---

# Wallet details

1. Reuse the user-selected existing profile. Only create a new wallet when requested; ask for contact email once and run the intact onboarding script with --new-agent NAME --email ADDRESS.
2. Configure X-Agent-Key privately from that profile in the MCP client. Endpoint: https://fluidnative.com/v1/mcp. Never put the credential in tool arguments, chat, or logs.
3. Call fluid_me with {}. Report its name, address, provider, network, chain ID, custody, verification and capabilities. The configured credential selects the wallet, not a name typed into chat.
4. Call fluid_balance with {"chain":"base"}. Report the returned ETH/USDC amounts and checkedAt timestamp separately from identity.
5. Explain missing metadata as unavailable, never invent balances or treat a network failure as zero. Legacy profiles may omit the new identity fields.
6. For device-owned profiles, hosted payment capability is false. Do not claim that registration or a funded balance enables sending. Local signing integration and spending authorization are separate.
7. On a failed query, preserve the profile and report the error. Resume pending setup with --resume-agent NAME; do not regenerate keys or switch to browser pairing automatically.

API equivalents: GET /v1/agents/me and GET /v1/agents/balance?chain=base.
SDK: await agent.getWalletDetails() returns { identity, balances } and fails if either query fails.
Guide: https://fluidnative.com/docs.md
Do not expose credential hashes, signing keys, raw agent credentials or local secret file contents.
