---
name: nft-buy
description: Buy NFTs on Base mainnet via a simple agent command
---

## Get an Agent Wallet (browser pairing and MCP)

Start at https://fluidnative.com/agents/start. The MCP server is https://fluidnative.com/v1/mcp; call `fluid_setup` without credentials for onboarding instructions. Configure `X-Agent-Key` privately in your MCP client after setup, then call `fluid_me` and `fluid_balance`. Never paste credentials into chat or tool arguments.

Use the intact raw script at https://fluidnative.com/scripts/fluid_agentic2.sh. If shell network access is blocked, an existing local script can prepare pairing offline: `bash fluid_agentic2.sh --pair-wallet cursor --email USER_CHOSEN_EMAIL`. The user signs in through the generated browser link and compares the key fingerprint before approving read-only access. Complete with `bash fluid_agentic2.sh --complete-pairing cursor` when permitted network access works. Keep the same local key on failure; do not bypass proxies or reconstruct Python from browser-extracted text.

Browser-only users can register and download a private profile at `/agents/start`, import with `--import-profile NAME --profile-file FILE`, then verify with `--profile NAME`. Never overwrite an existing profile. The same email connects to the same wallet; a separate wallet requires another verified email. `/agentic-keys` is key management, not registration. Do not silently redirect new-profile requests there.

Browser registration/pairing grants read-only access. Confirm chain, balance, gas, recipient, amount and explicit spending scope/limits before payments. Subscriptions require a supported recurring-payment mechanism plus service, amount, frequency, cap and expiry; onboarding does not configure them.


# nft-buy

Buy NFTs on Base mainnet via a simple agent command
