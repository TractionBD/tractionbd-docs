# PRD-001: Conversion Probability Prediction

**Product Requirements Document**
**Version:** 1.1
**Created:** January 21, 2026
**Last Updated:** January 21, 2026
**Author:** TractionBD Product Team
**Status:** Requirements Defined
**Feature ID:** TBDA-TBD (Post-MVP)

---

## Executive Summary

### Problem Statement

Business development professionals using TractionBD can see *who* to contact and *what* action to take, but they lack visibility into the *likelihood* of converting each prospect. This makes it difficult to:
- Prioritize high-probability opportunities when time is limited
- Set realistic expectations with leadership about pipeline health
- Understand which actions are actually moving prospects toward conversion
- Make data-driven decisions about where to invest effort

### Proposed Solution

Add a **Conversion Probability Score** that shows users a dynamic percentage likelihood of closing each prospect within a configurable timeframe. The probability updates based on actions taken, engagement signals, and historical conversion patterns from similar prospects.

### Value Proposition

| Stakeholder | Value |
|-------------|-------|
| **BD Professionals** | Prioritize time on highest-probability prospects; see tangible progress from actions |
| **Sales Managers** | Better pipeline forecasting; identify coaching opportunities |
| **Executives** | Reliable revenue predictions; data-driven resource allocation |
| **TractionBD Business** | Premium feature for higher-tier subscriptions; competitive differentiation |

### Origin

- **Requested by:** John C. Yates (Gunderson Dettmer) - investor/advisor
- **Endorsed by:** Gregg Bedol (CEO)
- **Technical Assessment:** Noelle Vest (Data/Analytics)

---

## Key Decisions (v1.1)

| Decision | Choice | Rationale |
|----------|--------|-----------|
| **Visual Design** | Meter/Gauge | Simple, universally understood; compact for Today List |
| **Default Timeframe** | 90 days | More stable predictions; sufficient data window |
| **NBC Integration** | Informational Only | Keep probability separate from ICP-based ranking |
| **PCR Scope** | Prospects Only | Focus on conversion-relevant contacts first |

---

## User Analysis

### Primary Users

#### Persona 1: "Alex the Account Executive"
- **Role:** Mid-level BD professional at a professional services firm
- **Goals:** Hit quarterly targets, maximize meeting-to-close ratio
- **Pain Points:**
  - Spends equal time on prospects with vastly different close likelihood
  - Can't demonstrate progress to leadership until deals close
  - Relies on gut feeling rather than data for prioritization
- **Job to Be Done:** "Help me focus on prospects most likely to convert so I hit my numbers"

#### Persona 2: "Morgan the Sales Manager"
- **Role:** Manages team of 5-10 BD professionals
- **Goals:** Accurate forecasting, team performance optimization
- **Pain Points:**
  - Pipeline reports are binary (open/closed) with no nuance
  - Hard to identify which reps need coaching on which prospects
  - Surprised by deals that stall or fall through
- **Job to Be Done:** "Give me visibility into true pipeline health so I can forecast accurately"

#### Persona 3: "Jordan the Solo Practitioner"
- **Role:** Independent consultant or small firm partner
- **Goals:** Efficient use of limited BD time
- **Pain Points:**
  - Limited time for business development alongside client work
  - No team to help qualify prospects
  - Needs to maximize ROI on every hour spent on BD
- **Job to Be Done:** "Tell me which prospects are worth my limited time"

### User Journey Impact

```
Current State:
┌─────────┐    ┌─────────┐    ┌─────────┐    ┌─────────┐
│ See     │───▶│ Take    │───▶│ Hope    │───▶│ Maybe   │
│ Contact │    │ Action  │    │ It Works│    │ Convert │
└─────────┘    └─────────┘    └─────────┘    └─────────┘
     ❓              ❓              ❓              ❓
  (Why them?)   (Will it help?)  (Am I close?)  (Why/why not?)

Future State:
┌─────────┐    ┌─────────┐    ┌─────────┐    ┌─────────┐
│ See     │───▶│ Take    │───▶│ See     │───▶│ Convert │
│ Contact │    │ Action  │    │ Progress│    │ or Learn│
│ + Prob% │    │         │    │ + Prob↑ │    │         │
└─────────┘    └─────────┘    └─────────┘    └─────────┘
     ✓              ✓              ✓              ✓
  (42% likely)  (Action boosts)  (Now 58%!)   (Model improves)
```

---

## Goals & Non-Goals

### Goals (In Scope)

| # | Goal | Success Metric |
|---|------|----------------|
| G1 | Show conversion probability for each prospect | 100% of prospects have a probability score |
| G2 | Update probability based on actions taken | Probability changes within 24h of logged action |
| G3 | Provide time-bounded predictions | User can select timeframe (30/60/90 days) |
| G4 | Display intuitive visual representation | >80% of users understand the visual in usability testing |
| G5 | Improve over time with outcome data | Model accuracy improves quarter-over-quarter |

### Non-Goals (Out of Scope for v1)

| # | Non-Goal | Rationale |
|---|----------|-----------|
| NG1 | Revenue amount prediction | Requires deal size data we don't collect in MVP |
| NG2 | Team-level probability aggregation | Enterprise feature for later release |
| NG3 | Automated actions based on probability | User trust must be established first |
| NG4 | Probability for non-prospect PCR types | **Decision:** Prospects only for v1 (Relationships, Past Clients, Current Clients excluded) |
| NG5 | Real-time probability updates | Batch updates sufficient for v1 |

### Success Criteria

1. **Adoption:** >60% of active users view probability scores weekly within 30 days of launch
2. **Accuracy:** Model achieves >65% AUC-ROC on held-out test set within 6 months
3. **Engagement:** Users with probability feature enabled log 20% more outcomes
4. **Satisfaction:** Feature receives >4.0/5.0 in user feedback surveys

---

## Functional Requirements

### FR1: Probability Calculation Engine

**Description:** Calculate conversion probability for each Prospect-type PCR based on multiple factors.

**Inputs:**
- ICP Fit Score (from TBDA-59)
- Relationship Warmth Score
- Contact Temperature (Hot/Warm/Cold)
- Action history (count, types, recency)
- Engagement signals (replies, meetings scheduled)
- Historical conversion patterns (from similar prospects)

**Output:**
- Probability percentage (0-100%)
- Confidence level (Low/Medium/High)
- Timeframe context (e.g., "within 90 days")

**Algorithm Approach (Initial):**
```
P(conversion | timeframe) = σ(β₀ + β₁·fit + β₂·warmth + β₃·actions + β₄·signals + β₅·similarity)

Where:
- σ = sigmoid function
- β weights learned from historical conversions
- similarity = weighted average of conversion rates for similar prospects
```

**Requirements:**
| ID | Requirement | Priority |
|----|-------------|----------|
| FR1.1 | Calculate probability for all Prospect-type PCRs | Must Have |
| FR1.2 | Support configurable timeframes (30/60/90 days) | Must Have |
| FR1.3 | Include confidence band based on data completeness | Must Have |
| FR1.4 | Update probabilities in nightly batch job | Must Have |
| FR1.5 | Recalculate on-demand when action is logged | Should Have |

### FR2: Probability Display

**Description:** Show probability to users in the Today List and PCR detail views.

**Selected Visual: Meter/Gauge** *(Decision: v1.1)*

```
┌─────────────────────────────────────────┐
│  Sarah Chen, VP Sales @ TechCorp        │
│                                         │
│  Conversion Probability                 │
│  ┌─────────────────────────────────┐    │
│  │▓▓▓▓▓▓▓▓▓▓▓▓▓▓░░░░░░░░░░░░░░░░░░│    │
│  └─────────────────────────────────┘    │
│       42% within 90 days                │
│       ↑ +8% from last week              │
│       ⚠ Low confidence (3 interactions) │
│                                         │
└─────────────────────────────────────────┘
```

**Design Rationale:**
- Simple, universally understood progress bar metaphor
- Clear percentage readout with timeframe context
- Trend indicator shows momentum (↑/↓/→)
- Confidence warning for data-limited prospects
- Compact enough for Today List cards

**Future Consideration:** Football Field visual may be explored in v2 based on user feedback.

**Requirements:**
| ID | Requirement | Priority |
|----|-------------|----------|
| FR2.1 | Display probability on Today List cards | Must Have |
| FR2.2 | Show probability trend (↑/↓/→) vs. last week | Should Have |
| FR2.3 | Display confidence indicator for low-data prospects | Must Have |
| FR2.4 | Allow user to change timeframe in settings | Should Have |
| FR2.5 | Show factors contributing to probability on hover/tap | Should Have |

### FR3: Outcome Tracking (Prerequisite)

**Description:** Capture conversion outcomes to train the probability model.

**Note:** This is a prerequisite that must be implemented before or alongside probability prediction.

**Requirements:**
| ID | Requirement | Priority |
|----|-------------|----------|
| FR3.1 | Allow user to mark prospect as "Converted" with date | Must Have |
| FR3.2 | Allow user to mark prospect as "Lost" with reason | Must Have |
| FR3.3 | Prompt for outcome when prospect inactive >90 days | Should Have |
| FR3.4 | Track time-to-conversion from first contact | Must Have |
| FR3.5 | Store outcome with full action history snapshot | Must Have |

### FR4: Model Learning & Improvement

**Description:** Continuously improve probability accuracy based on outcomes.

**Requirements:**
| ID | Requirement | Priority |
|----|-------------|----------|
| FR4.1 | Retrain model weekly with new outcome data | Must Have |
| FR4.2 | Track model accuracy metrics over time | Must Have |
| FR4.3 | Fall back to population baseline for cold-start users | Must Have |
| FR4.4 | Personalize model weights per user after 20+ outcomes | Should Have |
| FR4.5 | A/B test model improvements before rollout | Should Have |

---

## Non-Functional Requirements

### NFR1: Performance

| ID | Requirement | Target |
|----|-------------|--------|
| NFR1.1 | Probability calculation time | <100ms per prospect |
| NFR1.2 | Batch update for all prospects | <30 min nightly |
| NFR1.3 | UI render with probability | <200ms additional latency |

### NFR2: Accuracy & Reliability

| ID | Requirement | Target |
|----|-------------|--------|
| NFR2.1 | Model AUC-ROC (after 6 months) | >0.65 |
| NFR2.2 | Calibration error | <10% (predicted vs. actual) |
| NFR2.3 | Probability availability | 99.9% of prospects have score |

### NFR3: Privacy & Security

| ID | Requirement | Target |
|----|-------------|--------|
| NFR3.1 | User data isolation | Probabilities never cross user boundaries |
| NFR3.2 | Model training | Train on aggregated/anonymized patterns only |
| NFR3.3 | Explainability | Users can see why probability is what it is |

### NFR4: Scalability

| ID | Requirement | Target |
|----|-------------|--------|
| NFR4.1 | Support per user | Up to 10,000 prospects per user |
| NFR4.2 | Concurrent batch jobs | Support 1,000 users simultaneously |

---

## Acceptance Criteria

### AC1: Basic Probability Display

**Given** a user has prospects in their Today List
**When** they view the Today List
**Then** each prospect card shows:
- A probability percentage (0-100%)
- A visual meter/indicator
- The timeframe (e.g., "within 90 days")
- A confidence indicator if data is limited

### AC2: Probability Updates with Actions

**Given** a user logs an action for a prospect (email, call, meeting)
**When** the next batch job runs (or on-demand refresh)
**Then** the prospect's probability is recalculated
**And** the new probability reflects the positive signal from the action

### AC3: Outcome Logging

**Given** a user wants to record a conversion outcome
**When** they mark a prospect as "Converted" or "Lost"
**Then** the system records:
- Outcome type and date
- Time from first contact to outcome
- Full action history snapshot
**And** the prospect is removed from active probability calculations

### AC4: Timeframe Selection

**Given** a user wants to see shorter-term predictions
**When** they change their probability timeframe setting to 30 days
**Then** all probability scores recalculate for the 30-day horizon
**And** the UI reflects the new timeframe

### AC5: Low Confidence Handling

**Given** a prospect has limited data (< 3 interactions, no signals)
**When** displaying their probability
**Then** the system shows a "Low Confidence" indicator
**And** explains what data would improve confidence
**And** uses conservative (population baseline) estimates

### AC6: Model Accuracy Tracking

**Given** the system has been running for 3+ months with outcomes
**When** measuring model performance
**Then** AUC-ROC should be >0.60 (target: >0.65)
**And** calibration plots should show reasonable alignment

---

## Dependencies & Prerequisites

### Must Complete Before This Feature

| Dependency | Status | Owner | Notes |
|------------|--------|-------|-------|
| MVP Launch | In Progress | Engineering | Need user base for outcome data |
| Outcome Tracking (FR3) | Not Started | Engineering | Required for model training |
| 3+ months of outcome data | Not Started | N/A | Need statistical significance |

### Technical Dependencies

| System | Integration Point |
|--------|-------------------|
| ICP Scoring (TBDA-59) | Input: fit_score |
| Warmth Decay Algorithm | Input: warmth_score |
| Action Logging | Input: action history |
| Signal System | Input: engagement signals |
| Nightly Batch Jobs | Execution: probability refresh |

---

## Implementation Phases

### Phase 0: Foundation (MVP+1 month)
- Implement Outcome Tracking (FR3)
- Begin collecting conversion/loss data
- Design data schema for probability storage

### Phase 1: Initial Model (MVP+3 months)
- Build baseline probability model
- Implement probability calculation engine (FR1)
- Add basic probability display (FR2 - one visual option)

### Phase 2: Refinement (MVP+4 months)
- Add timeframe selection
- Implement probability trend indicators
- Add factor explainability on hover

### Phase 3: Personalization (MVP+6 months)
- Per-user model personalization
- A/B testing framework for model improvements
- Advanced visualizations based on user feedback

---

## Open Questions

| # | Question | Status | Decision |
|---|----------|--------|----------|
| Q1 | Which visual representation do users prefer? | **Resolved** | **Meter/Gauge** - simple, universally understood |
| Q2 | Default timeframe: 30, 60, or 90 days? | **Resolved** | **90 days** - more stable predictions |
| Q3 | Should probability affect NBC ranking? | **Resolved** | **No** - keep separate from ICP fit; probability is informational only |
| Q4 | Minimum outcomes needed before personalization? | Open | Propose 20 |
| Q5 | Should we show probability for Relationships too? | **Resolved** | **Prospects only** for v1 |

---

## Appendix

### A. Related Documents

- [Core AI & Algorithm Backend Functionality v2.0](../Core_AI_and_Algorithm_Backend_Functionality_v2.md)
- Feature #13: Analytics & Attribution (Future roadmap)

### B. Competitive Analysis

| Competitor | Probability Feature | Visual |
|------------|---------------------|--------|
| Salesforce Einstein | Lead Score (0-100) | Score badge |
| HubSpot | Deal Probability % | Progress bar |
| Pipedrive | Win probability | Percentage |
| Gong | Deal likelihood | Risk indicators |

### C. Change Log

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | Jan 21, 2026 | Product Team | Initial draft |
| 1.1 | Jan 21, 2026 | Product Team | **Refinement:** Resolved Q1-Q3, Q5. Selected Meter/Gauge visual, 90-day default timeframe, Prospects-only scope, informational-only (no NBC impact). Added Key Decisions section. |

---

## Sign-Off

| Role | Name | Date | Status |
|------|------|------|--------|
| Product | | | Pending |
| Engineering | | | Pending |
| Data/Analytics | Noelle Vest | | Pending |
| Executive Sponsor | Gregg Bedol | | Pending |
