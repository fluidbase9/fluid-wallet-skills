---
name: authenticate
description: Create a device-owned Fluid agent wallet with one contact-email input, or resume/connect a user-selected existing wallet.
---

# Fluid agent onboarding

For a requested NEW wallet, ask for contact email first if it was not already provided. Use the client's free-text input tool when available, with the question: "What contact email should I use for this agent wallet?" Do not substitute a multiple-choice setup question for the email field. If no text-input tool exists, ask the same question in chat and wait for the answer. Never invent an email. A website or MCP server cannot force an input box in every client.

Email is unverified contact metadata for this device-owned flow. It need not be unused; the same email may label separate wallets. Do not require email verification, password, seed phrase, or browser pairing. Do not test an unrelated environment credential as a prerequisite for creating the requested new wallet.

1. If connected, call MCP `fluid_setup` at https://fluidnative.com/v1/mcp without credentials for guidance. It returns instructions, not a newly created local wallet.
2. Download and inspect the intact raw script:
   `curl -fsSL https://fluidnative.com/scripts/fluid_agentic2.sh -o fluid_agentic2.sh`
3. Run it yourself with an unused profile name and the supplied email:
   `bash fluid_agentic2.sh --new-agent claude --email USER_EMAIL`
   Requires Node.js 20+. Use appropriate shell quoting for user input. Examples of profile names: chatgpt, claude, cursor. Never overwrite an existing profile; resume it only when that is the requested wallet.
4. If interrupted, preserve keys and resume the same profile with `bash fluid_agentic2.sh --resume-agent claude`. Never use legacy `--new-wallet` or `--profile` for this device-owned flow.
5. After verified setup, configure X-Agent-Key privately from that profile, then call `fluid_me` and `fluid_balance`. Never print keys or profile contents. A private device-only key reveal is separate from chat.
6. Report address, Base chain ID 8453, contact email verification status, naming status and current balance timestamp. A pending ENS name does not imply wallet registration failed. Hosted payments remain unsupported for this profile; no spending is authorized by setup.

For an existing-wallet request, use the explicitly selected saved profile and verify it; do not create another. Browser/legacy pairing is an explicit alternative only. On continued HTTP/network errors, stop with the exact failed step; do not bypass restrictions, infer invalid credentials from every 403, or regenerate keys.

Machine-readable guide: https://fluidnative.com/docs.md
Developer guide: https://fluidnative.com/agent-quickstart.html


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
