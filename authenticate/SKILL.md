---
name: authenticate
description: Get a Fluid Wallet agent key (fwag_...) so you can send, swap, and pay on Base.
---

# Authenticate with Fluid Wallet

Before using any Fluid Wallet skills, you need an agent key. This skill guides you through getting one.

## What You Need

A Fluid Wallet account at https://fluidnative.com and an agent key (format: `fwag_...`).

## Steps (Human + Agent)

### Step 1 — Human: Create an account
1. Go to **https://fluidnative.com**
2. Click **Get Started** → Register
3. Enter email and password
4. Save your 12-word seed phrase (shown once — write it down)
5. Scan the Google Authenticator QR code with your phone
6. Enter the 6-digit OTP to complete registration

### Step 2 — Human: Create an agent key
1. Go to **https://fluidnative.com/agentic-keys**
2. Click **New Key**
3. Name it (e.g. "My Agent")
4. Select scopes: `read`, `pay`, `swap`, `agentpay`
5. Set per-transaction limit (e.g. $100) and daily limit (e.g. $500)
6. Copy the `fwag_...` key — it is shown **once only**

### Step 3 — Agent: Store the key
```bash
export FLUID_AGENT_KEY=fwag_...
```
Or add to `.env`:
```
FLUID_AGENT_KEY=fwag_...
```

### Step 4 — Agent: Verify it works
```bash
curl https://fluidnative.com/v1/agents/me \
  -H "X-Agent-Key: $FLUID_AGENT_KEY"
```
Expected response:
```json
{
  "email": "you@example.com",
  "walletAddress": "0x...",
  "scopes": ["read", "pay", "swap", "agentpay"]
}
```

## SDK Setup (optional)
```bash
npm install fluid-wallet-agentkit
```
```typescript
import { FluidAgent } from 'fluid-wallet-agentkit';
const agent = new FluidAgent({ apiKey: process.env.FLUID_AGENT_KEY });
const me = await agent.getMe();
console.log(me.walletAddress);
```

## Notes
- Agent key is tied to YOUR wallet — the agent spends YOUR funds
- Transactions under $100 execute automatically
- Transactions over $100 send an approval email to you before executing
- Keys can be revoked at any time from https://fluidnative.com/agentic-keys
