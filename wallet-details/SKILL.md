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


## Required completion summary
After registration, query fluid_me and fluid_balance (or the equivalent authenticated API calls) and present a readable wallet table: profile/agent type, contact email and verification, username, public agentId or pending naming status, wallet address, provider/custody, network name, chain ID/type, gas token, ETH/USDC token names/types/decimals, USDC contract, scopes/capabilities, live balances and check timestamp, and explorer link. If a balance lookup fails, report unavailable; registration may still be verified. Gas balance is not a transaction fee estimate.
A request for "full ID" means the public agent ID or wallet address, never the private fwag_local_ API credential. Do not suggest revealing a secret to answer an identity question. If agentId is absent, report the returned naming status; do not invent an ENS registration.


## Default wallet creation report
After every successful new-wallet registration or resume, return a short verified-status sentence followed by a two-column Markdown table (Field | Value). Do not wait for the user to request details. Include every row below, using the current profile and authenticated responses, never sample values:

| Field | Value source |
| --- | --- |
| Profile | Selected local profile |
| Agent type | Identity agentType; unavailable if absent |
| Contact email | contactEmail and emailVerified |
| Username | username; note that the email alias is not unique |
| Public agent ID | agentId or not assigned |
| Naming status | naming.status or unavailable |
| ENS resolution | naming.ensResolution; distinguish a usable payment name from a pending name |
| Wallet address | Verified walletAddress |
| Provider | Identity provider |
| Custody | Identity custody and signing model |
| Network | network.name |
| Chain ID / type | network.chainId and network.architecture |
| Gas token | Native token name, symbol, type and decimals |
| USDC | Token name, type, decimals and contract address |
| Scopes | Identity scopes |
| Capabilities | Enabled and unsupported capabilities |
| ETH balance | Live Base ETH balance or unavailable |
| USDC balance | Live Base USDC balance or unavailable |
| Balance checked | Response checkedAt; otherwise label the client query time explicitly |
| Explorer | Verified wallet address's explorer link |

End with: keys stay on the device and are not included in chat; registration does not fund the wallet or authorize spending; gas balance is not a transaction fee estimate. If naming is pending, receive funds using the verified address, not the proposed name. Never interpret "full ID" as a private key. If registration fails, show a failure/pending report instead of the verified-success sentence. A failed balance request does not undo verified registration. Missing metadata must be labeled unavailable, not guessed.
