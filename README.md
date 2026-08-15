# SponsorSync — Decentralized Creator Economy Sponsorship Verification Protocol

> **"Sponsorship payouts released on proof, not promises."**

An Intelligent Contract protocol built on **GenLayer** that eliminates creator fraud and automates milestone escrow payouts for creator-economy sponsorships.

---

## 🌟 The Core Problem

Brands lose millions annually to four primary creator fraud vectors:
1. **Burner Accounts**: Delivering campaigns on empty or fake clone channels.
2. **Bot Farms**: Using purchased views, fake likes, and emoji spam to simulate performance.
3. **URL Theft**: Submitting someone else's viral video link.
4. **Delete & Dash**: Creators deleting or privating sponsored videos immediately after payment clears.

Traditional deterministic oracles (e.g. Chainlink) cannot evaluate transcripts, verify channel authority, or judge sentiment. Human compliance agencies are slow, expensive, and opaque. **SponsorSync solves this by using GenLayer's decentralized AI consensus committee to verify performance before triggering staged escrow releases on EVM chains.**

---

## 🛡️ The 4 Novel Anti-Fraud Differentiators (Why SponsorSync is Unique)

Unlike naive sponsorship checkers (e.g., ProofSponsor), SponsorSync introduces four robust, un-gameable security layers:

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
   - Rejects burner channels created <30 days ago or below brand-configured subscriber/view thresholds.
2. **Layer 2 — Bot-Farm Detection via Semantic Comment Analysis**:
   - AI committee analyzes top comments for contextual discussion (e.g., `"Loved the pump design at 04:12"`) vs bot spam (`"🔥🔥🔥"`, `"Nice video"`), combined with anomalous engagement ratio checks.
3. **Layer 3 — Cryptographic Channel Binding (`GL-VERIFY` Claim Code)**:
   - On registration, the contract generates a unique claim code (e.g., `GL-VERIFY-8F3K2`) bound to the creator's wallet. The creator must include this in the video description, proving channel control and preventing URL theft.
4. **Layer 4 — Delete-&-Dash Temporal Staged Escrow**:
   - **Day 0 (Initial Audit)**: 50% USDC released upon full compliance.
   - **Day 7 (Retention Audit)**: GenLayer re-audits the URL. If public and intact, remaining 50% releases; if deleted/privated, remaining funds are clawed back to the brand.

---

## 🏗️ Technical Architecture & Separation of Concerns

```
┌─────────────────────────────────────────────────────────────┐
│                    LAYER 1: FRONTEND UI                     │
│    Next.js + Tailwind (Brand Portal, Creator Portal, Panel) │
└──────────────────────────────┬──────────────────────────────┘
                               │ GenLayer SDK / RPC
┌──────────────────────────────▼──────────────────────────────┐
│            LAYER 2: GENLAYER INTELLIGENT CONTRACT           │
│                    SponsorSyncCourt.py                      │
│   • gl.nondet.web.render() scraping                         │
│   • gl.eq_principle.prompt_non_comparative()                │
│   • Deterministic Python Anti-Fraud Verdict Engine          │
│   • 7-Day Temporal Retention State Machine                  │
└──────────────────────────────┬──────────────────────────────┘
                               │ Emits On-Chain Verdicts
┌──────────────────────────────▼──────────────────────────────┐
│                 LAYER 3: EVM VALUE SETTLEMENT               │
│                    SponsorSyncEscrow.sol                    │
│   • Holds USDC on Base/Arbitrum                             │
│   • Releases 50% Tranche 1 on INITIAL_APPROVED              │
│   • Releases 50% Tranche 2 on FULLY_SETTLED                 │
│   • Claws back 50% on CLAWBACK_TRIGGERED                    │
└─────────────────────────────────────────────────────────────┘
```

---

## 🚀 Step-by-Step GenLayer Studio Testing Guide

### 1. Deploy Contract
Deploy `SponsorSyncCourt` with your wallet address as `operator`.

### 2. Create Campaign (`create_campaign`)
* `required_handle`: `"@MrBeast"`
* `platform`: `"YOUTUBE"`
* `min_subscribers`: `1000000`
* `min_avg_views`: `500000`
* `brief_requirements`: `"Must feature GenLayer sponsorship in first 3 minutes, show promo code MRBEAST, and leave link in description."`
* `escrow_amount_usdc`: `5000`
> *Returns: `"SPONSOR_CAMPAIGN_001"`*

### 3. Register Creator (`register_creator`)
* `campaign_id`: `"SPONSOR_CAMPAIGN_001"`
> *Returns Unique Claim Code: `"GL-VERIFY-8F3K2"`*

### 4. Submit Evidence (`submit_evidence`)
* `campaign_id`: `"SPONSOR_CAMPAIGN_001"`
* `video_evidence_url`: `"https://metaremover.github.io/demo/youtube_perfect.html"`

### 5. Run Initial Audit (`run_initial_audit`)
* `campaign_id`: `"SPONSOR_CAMPAIGN_001"`
> *Result: `status: "INITIAL_APPROVED"`, `verdict: "FULL_COMPLIANCE"`, `tranche_1_released: true`.*

### 6. Run Retention Audit (`run_retention_audit`)
* `campaign_id`: `"SPONSOR_CAMPAIGN_001"`
> *Result: `status: "FULLY_SETTLED"`, `tranche_2_released: true`.*
