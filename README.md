# SponsorSync — Decentralized Creator Economy Sponsorship Verification Protocol

> **"Sponsorship payouts released on proof, not promises."**

An Intelligent Contract protocol built on **GenLayer** that eliminates creator fraud and automates milestone escrow payouts for creator-economy sponsorships.

---

## 🔗 Live Deployment & Repository Links

- **GenLayer Explorer Contract**: [`0x6FC89A7FcA83401dbe04E502d0053e7074aAB68D`](https://explorer-studio.genlayer.com/address/0x6FC89A7FcA83401dbe04E502d0053e7074aAB68D)
- **GitHub Repository**: [`https://github.com/tumhi4/sponsorsync`](https://github.com/tumhi4/sponsorsync)
- **Live Frontend Portal**: [`https://sponsor-sync-demo.vercel.app/`](https://sponsor-sync-demo.vercel.app/)

---

## 🌟 The Core Problem

Brands lose millions annually to four primary creator fraud vectors:
1. **Burner Accounts**: Delivering campaigns on empty or fake clone channels.
2. **Bot Farms**: Using purchased views, fake likes, and emoji spam to simulate performance.
3. **URL Theft**: Submitting someone else's viral video link.
4. **Delete & Dash**: Creators deleting or privating sponsored videos immediately after payment clears.

Traditional deterministic oracles cannot evaluate transcripts or verify channel authority. **SponsorSync solves this by using GenLayer's decentralized AI consensus committee to verify performance before triggering staged escrow releases on EVM chains.**

---

## 🛡️ The 4 Novel Anti-Fraud Differentiators

```
+--------------------------------------------------------------------------------------------------+
|                                  SPONSORSYNC 4-LAYER FRAUD SHIELD                                 |
+--------------------------------------------------------------------------------------------------+
| [Layer 1: Channel Authority Gate]        -> Enforces min subscribers, min views, channel age.     |
| [Layer 2: Semantic Bot-Farm Detection]   -> Inspects top comments for contextual discussion vs spam.|
| [Layer 3: Cryptographic Channel Binding] -> Issues unique GL-VERIFY claim code bound to creator. |
| [Layer 4: Delete-&-Dash Temporal Escrow] -> 50% Day 0 release + 50% Day 7 retention audit/clawback.|
+--------------------------------------------------------------------------------------------------+
```

1. **Layer 1 — Channel Authority & History Gate**:
   - Rejects burner channels created <30 days ago or below brand-configured subscriber/view thresholds (`INSUFFICIENT_CHANNEL_AUTHORITY`).
2. **Layer 2 — Bot-Farm Detection via Semantic Comment Analysis**:
   - AI committee analyzes top comments for contextual discussion vs bot spam, combined with like/view ratio anomaly checks (`SUSPECTED_BOT_ACTIVITY`).
3. **Layer 3 — Cryptographic Channel Binding (`GL-VERIFY` Claim Code)**:
   - On registration, the contract generates a unique claim code (e.g., `GL-VERIFY-855736`) bound to the creator's wallet. The creator must include this in the video description to prove channel control (`MISSING_CLAIM_CODE`).
4. **Layer 4 — Delete-&-Dash Temporal Staged Escrow**:
   - **Day 0 (Initial Audit)**: 50% USDC released upon full compliance.
   - **Day 7 (Retention Audit)**: GenLayer re-audits the URL. If public and intact, remaining 50% releases; if deleted/privated, remaining funds are clawed back to the brand.

---

## 🏗️ Technical Architecture & Verified Settlement Relay

```
┌─────────────────────────────────────────────────────────────┐
│                    LAYER 1: FRONTEND UI                     │
│    Next.js + Tailwind (Method-Matched GenLayer JSON-RPC)    │
└──────────────────────────────┬──────────────────────────────┘
                               │ GenLayer RPC (gen_sendTransaction / gen_callView)
┌──────────────────────────────▼──────────────────────────────┐
│            LAYER 2: GENLAYER INTELLIGENT CONTRACT           │
│                    SponsorSyncCourt.py                      │
│   • gl.nondet.web.render() DOM scraping                     │
│   • Asymmetric Equivalence Consensus Committee              │
│   • Sets tranche_1_released / tranche_2_released state      │
└──────────────────────────────┬──────────────────────────────┘
                               │ Polls Verdict (get_campaign)
┌──────────────────────────────▼──────────────────────────────┐
│            LAYER 3: VERIFIED SETTLEMENT RELAY               │
│               relay/SponsorSyncRelay.py                     │
│   • Reads GenLayer Court verdict and release flags          │
│   • Calls releaseTranche1 / releaseTranche2 / claimRefund   │
└──────────────────────────────┬──────────────────────────────┘
                               │ EVM Transactions
┌──────────────────────────────▼──────────────────────────────┐
│             LAYER 4: EVM STAGED ESCROW CONTRACT             │
│                    SponsorSyncEscrow.sol                    │
│   • Holds 100% USDC performance escrow on Base/Arbitrum     │
│   • Disburses 50% on Tranche 1 and 50% on Tranche 2         │
└─────────────────────────────────────────────────────────────┘
```

---

## 🚀 Running the Verified Settlement Relay

```bash
# Set environment variables
export GENLAYER_RPC="https://studio.genlayer.com/api"
export GENLAYER_COURT_ADDRESS="0x6FC89A7FcA83401dbe04E502d0053e7074aAB68D"
export EVM_RPC_URL="https://sepolia.base.org"
export EVM_ESCROW_ADDRESS="0x3Fa9b23f81902c34918239482910394817e12a89"

# Run the settlement relay
python3 relay/SponsorSyncRelay.py SPONSOR_CAMPAIGN_002
```

---

## 🛠️ Step-by-Step GenLayer Studio Testing Guide

### 1. Deploy Contract
Deploy `SponsorSyncCourt.py` in Studio with your wallet as `operator`.

### 2. Create Campaign (`create_campaign`)
* `creator_address`: `"0x71546f55c131acd54cf93e181b9cabaeaf440fc3"`
* `required_handle`: `"@MrBeast"`
* `platform`: `"YOUTUBE"`
* `min_subscribers`: `1000000`
* `min_avg_views`: `500000`
* `brief_requirements`: `"Feature GenLayer sponsorship in first 3 minutes and display promo code MRBEAST"`
* `escrow_amount_usdc`: `5000`
> *Returns: `"SPONSOR_CAMPAIGN_002"`*

### 3. Register Creator (`register_creator`)
* `campaign_id`: `"SPONSOR_CAMPAIGN_002"`
> *Returns Unique Claim Code: `"GL-VERIFY-855736"`*

### 4. Submit Evidence (`submit_evidence`)
* `campaign_id`: `"SPONSOR_CAMPAIGN_002"`
* `video_evidence_url`: `"https://sponsor-sync-demo.vercel.app/youtube_perfect.html"`

### 5. Run Initial Audit (`run_initial_audit`)
* `campaign_id`: `"SPONSOR_CAMPAIGN_002"`
> *Result: `status: "INITIAL_APPROVED"`, `verdict: "FULL_COMPLIANCE"`, `tranche_1_released: true`.*

### 6. Run Retention Audit (`run_retention_audit`)
* `campaign_id`: `"SPONSOR_CAMPAIGN_002"`
> *Result: `status: "FULLY_SETTLED"`, `tranche_2_released: true`.*
