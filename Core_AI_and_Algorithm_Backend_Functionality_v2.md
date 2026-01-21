# Core AI & Algorithm Backend Functionality

**TractionBD Technical Architecture**
**Version 2.1 | January 2026**
*Updated with December 2025 Architecture Decisions + Conversion Probability Feature*

---

## What's New in This Version

- **Two-System Architecture**: Explicit NBC (Next Best Contact) + NBA (Next Best Action) separation
- **Additive Distance Scoring Model (TBDA-59)**: Five-dimension ICP matching with configurable weights
- **Weight Learning via Log-Loss (TBDA-60)**: System learns from user classification overrides
- **Contact Temperature Model**: Hot/Warm/Cold classification affecting priority
- **PCR Type Priority Weighting**: Past Clients prioritized over Prospects over Relationships
- **Recency Rules Framework**: Action-type-specific minimum wait times to prevent spam
- **Reaction vs Action Framework**: Signals that bypass normal ranking rules
- **Just-in-Time Research**: Automated lookup for top-ranked contacts (rate-limited by tier)
- **Confidence Bands**: Low/Medium/High affecting action aggressiveness
- **MVP vs Future Vision**: Clear delineation of what ships now vs. later
- **NEW: Conversion Probability Prediction (Feature #20)**: Time-bounded probability of closing each prospect

---

# PART 1: EXECUTIVE SUMMARY

*This section provides a business-friendly overview of TractionBD's AI and algorithmic capabilities. Technical implementation details are in Part 2.*

## 1.1 The Two-System Architecture: NBC + NBA

TractionBD uses two separate but connected systems to help professionals manage their business development:

| System 1: NBC (Next Best Contact) | System 2: NBA (Next Best Action) |
|-----------------------------------|----------------------------------|
| **Purpose:** Determines WHO to contact today | **Purpose:** Determines WHAT action to take |
| **Output:** Top 3-7 contacts for Today List | **Output:** Recommended action type + contextual CTA |
| **Considers:** ICP fit, relationship warmth, recency, signals, contact type | **Considers:** Last action, signals, relationship stage, user preferences |

**Key insight:** These systems run sequentially. First, NBC selects the most important contacts. Then, NBA determines the best action for each selected contact.

## 1.2 How Contact Prioritization Works

The system considers multiple factors when deciding who should appear in your Today List:

### ICP Fit Score

How well does this contact match your Ideal Client Profile? The system compares each contact against five dimensions you define: industry, company size, seniority level, company growth trajectory, and industry growth trends. Better matches score higher.

### Relationship Warmth

How healthy is this relationship right now? The system tracks recency (when did you last interact?), frequency (how often do you engage?), and responsiveness (do they reply?). Warmth naturally decays over time, creating urgency to re-engage before relationships go cold.

### Contact Temperature

A quick classification based on recent engagement patterns:

| Temperature | Criteria |
|-------------|----------|
| **Hot** | Recent contact AND they responded |
| **Warm** | Recent contact without response, OR older contact with high ICP fit |
| **Cold** | Old contact AND low engagement history |

### Contact Type Priority

Not all contacts are equal. The system applies different priority weights based on relationship type:

- **Past Clients** (highest priority) - Best source of referrals and repeat business
- **Prospects** (high priority) - Active pipeline opportunities
- **Relationships** (medium priority) - Network maintenance
- **Current Clients** (lower priority) - Already engaged, less outreach needed

## 1.3 Reactions vs. Actions: When Rules Get Bypassed

The system distinguishes between proactive outreach ("Actions") and time-sensitive responses ("Reactions"):

| Actions (Normal Flow) | Reactions (Override Flow) |
|-----------------------|---------------------------|
| Follow normal ranking rules | Bypass recency rules |
| Respect recency minimums | Elevated to top of Today List |
| Examples: Check-ins, value shares, reconnects | Triggers: Inbound reply, meeting in 48h, funding announcement, job change |

Reactions are time-sensitive opportunities that shouldn't be missed. When a signal triggers a Reaction, it appears in your Today List regardless of when you last contacted that person.

## 1.4 Preventing Over-Contact: Recency Rules

To maintain professional credibility, the system enforces minimum wait times between touches. These vary by action type and your outreach style preference:

| Action Type | Conservative | Aggressive |
|-------------|--------------|------------|
| Email | 14 days minimum | 10 days minimum |
| Phone Call | 7 days minimum | 5 days minimum |
| LinkedIn Message | 10 days minimum | 7 days minimum |
| Meeting Request | 14 days minimum | 10 days minimum |
| Text Message | 10 days minimum | 7 days minimum |

*(Note: These are proposed defaults. Users select Conservative or Aggressive during onboarding.)*

## 1.5 How the System Learns Your Preferences

TractionBD learns from your behavior in several ways:

### Classification Overrides

When you classify a contact as "High Fit" but the system scored them lower, the system adjusts its scoring weights to better match your judgment. Over time, this personalizes the scoring model to your specific business.

### Action Patterns

The system tracks which recommended actions you accept vs. snooze vs. dismiss. If you consistently dismiss a certain action type, it learns to suggest alternatives.

### Message Editing

When you edit AI-generated drafts before sending, the system learns your preferred tone, length, and style. Heavy edits signal that the voice profile needs refinement.

### Outcome Tracking

When actions lead to meetings, proposals, or revenue, the system strengthens the patterns that led to those outcomes.

## 1.6 Complete Feature Catalog

The following table summarizes all AI and algorithmic capabilities. MVP features ship in the initial release; Future features are planned for subsequent versions.

### Core Capabilities (1-10)

| # | Feature | What It Does | How It Works | Status |
|---|---------|--------------|--------------|--------|
| 1 | Voice Capture & Understanding | Converts spoken input to structured data (new contacts, meeting notes, follow-ups) | Speech-to-text + NLU for intent/entity extraction + LLM summarization | MVP |
| 2 | LLM Orchestration | Manages all AI calls with retries, routing, guardrails, and caching | Prompt templates + context injection + model routing + output validation | MVP |
| 3 | PCR Capture & Enrichment | Parses contact inputs, deduplicates, and enriches with external data | Entity resolution + fuzzy matching + enrichment APIs + field normalization | MVP |
| 4 | ICP Scoring (TBDA-59) | Scores each contact against your Ideal Client Profile across 5 dimensions | Additive distance model with configurable weights; user overrides feed learning | MVP |
| 5 | Daily Prioritization (NBC) | Ranks contacts daily to determine Today List; applies type weighting and recency rules | Composite scoring + temperature + type multipliers + cadence filtering | MVP |
| 6 | Next Best Action (NBA) | Selects the optimal action type for each prioritized contact | Rule-based engine with priority hierarchy; LLM generates contextual CTAs | MVP |
| 7 | Just-in-Time Research | Retrieves timely signals (news, job changes, funding) for top contacts only | Automated lookup (rate-limited by tier) for top 3-7 contacts before Today List display | MVP |
| 8 | Message Generation | Generates personalized email/LinkedIn drafts matching your voice | LLM with voice profile + context injection + guardrails; learns from edits | MVP |
| 9 | Behavioral Learning | Learns from your edits, classification overrides, and action patterns | Log-loss weight updates (TBDA-60) + edit distance tracking + outcome correlation | MVP |
| 10 | Data Integration | Ingests data from CSV imports, manual entry, and logged actions | Schema mapping + deduplication + conflict resolution + identity graph | MVP |

### Extended Capabilities (11-20)

| # | Feature | What It Does | How It Works | Status |
|---|---------|--------------|--------------|--------|
| 11 | ICP Auto-Discovery | Refines your Ideal Client Profile based on who actually converts | Clustering + feature importance analysis + suggestion engine | Future |
| 12 | Meeting Prep & Coach | Assembles briefings before meetings with talking points and suggested questions | Context retrieval + LLM summarization + conversation simulation | Future |
| 13 | Analytics & Attribution | Ties actions to outcomes (meetings, revenue) and reports effectiveness | Attribution models + cohort analysis + churn prediction | Future |
| 14 | A/B Testing Framework | Tests subject lines, cadences, and actions; auto-promotes winners | Experiment orchestration + multi-armed bandits for fast convergence | Future |
| 15 | Firm Relationship Graph | For multi-user deployments: who knows whom, conflict checking, collaboration suggestions | Graph database + centrality algorithms + conflict detection rules | Future |
| 16 | Privacy & Compliance | Enforces data residency, PII redaction, and audit trails | PII detection models + policy engines + anomaly detection | Future |
| 17 | Cross-Professional Benchmarks | Anonymized benchmarks: typical cadences, response rates, referral sources | Aggregation pipelines + statistical benchmarking + outlier detection | Future |
| 18 | Model Monitoring | Tracks latency, accuracy, and quality across all AI components | Monitoring dashboards + drift detection + auto-evaluation scripts | Future |
| 19 | Cold-Start Onboarding | Collects preferences during onboarding; initializes scoring without history | Pairwise comparisons + initial weight derivation + gradual transition to behavioral weights | MVP |
| 20 | **Conversion Probability Prediction** | Shows likelihood of closing each prospect within configurable timeframe (30/60/90 days) | Logistic regression on fit + warmth + actions + signals; learns from outcomes | Future |

*Features 11-18 and 20 represent the long-term vision for TractionBD. They build on the MVP foundation and will be prioritized based on user feedback and business outcomes.*

---

# PART 2: TECHNICAL APPENDIX

*This section provides implementation details, algorithms, and technical specifications for engineering teams.*

## 2.1 Scoring System Architecture (TBDA-59)

### Additive Distance Model

The ICP Fit score uses an additive distance model:

```
score = Σ wₖ × (1 - |prospect_k - icp_k| / max_distance_k)
```

Where:
- `wₖ` = weight for dimension k
- `prospect_k` = prospect's value on dimension k
- `icp_k` = user's ideal value on dimension k
- `max_distance_k` = maximum possible distance on dimension k

### Scoring Dimensions (Initial Configuration)

| Dimension | Default Weight | Description |
|-----------|----------------|-------------|
| NAICS (Industry) | 30 | Industry code match using NAICS hierarchy; partial credit for related industries |
| Company Size | 20 | Employee count bands (1-10, 11-50, 51-200, 201-500, 500+); adjacent bands get partial credit |
| Seniority | 20 | Role level (C-Suite, VP, Director, Manager, IC); maps to seniority score 1-5 |
| Company Growth | 20 | YoY headcount or revenue growth rate; higher growth = higher score |
| Industry Growth | 10 | Sector growth trends; hot industries score higher than declining ones |

*Note: Weights are configurable and will be personalized through weight learning (TBDA-60). Initial values derived from CRM import analysis or onboarding questionnaire.*

### User Classification vs. System Score

MVP displays user-assigned classifications (High/Medium/Low) rather than numeric scores:

- **User sees:** High Fit / Medium Fit / Low Fit badges
- **System calculates:** Numeric score (0-100) in background
- **Both stored:** User classification + system score preserved for learning
- **Discrepancies feed weight learning:** User says High, system says 45 → adjust weights

## 2.2 Weight Learning via Log-Loss (TBDA-60)

When a user overrides the system's classification, weight learning adjusts dimension weights to better align with user preferences.

### Trigger Conditions

Weight learning activates when:
- User classifies a PCR differently than the system would suggest
- User accepts/rejects action recommendations over time
- Outcomes (meetings, revenue) are attributed to PCRs

### Learning Mechanism

Uses log-loss optimization via gradient descent:

1. **Loss Calculation:** Compare predicted classification probability vs. user's actual classification
2. **Gradient Computation:** Calculate gradient of loss with respect to each dimension weight
3. **Weight Update:** Adjust weights in direction that reduces loss
4. **Regularization:** Prevent overfitting to small sample sizes

### Execution Timing

- **Runs:** Nightly batch job (same time as score refresh)
- **Start:** Learning begins from first user classification (small learning rate)
- **Learning Rate:** Starts small (0.01), gradually increases as classification count grows
- **Convergence:** Weights stabilize after ~50-100 classifications

*Rationale for starting from first classification: Even one data point provides signal. With a very small learning rate, the risk of overfitting to noise is minimal, and users see the system becoming more personalized immediately.*

## 2.3 NBC Algorithm (Next Best Contact)

The NBC algorithm determines which contacts appear in the Today List.

### Priority Score Calculation

For each PCR, calculate:

```
priority = (fit_score × fit_weight) + (warmth_score × warmth_weight) + (strategic_score × strategic_weight)

adjusted_priority = priority × type_multiplier × temperature_boost
```

### PCR Type Multipliers

| PCR Type | Multiplier | Rationale |
|----------|------------|-----------|
| Past Client | 1.2x | Best referral source + repeat business potential |
| Prospect | 1.1x | Active pipeline opportunity |
| Relationship | 0.9x | Network maintenance (lower urgency) |
| Current Client | 0.8x | Already engaged; less outreach needed |

### Temperature Classification

Contact temperature affects priority boost. Thresholds are configurable:

| Temperature | Criteria | Effect |
|-------------|----------|--------|
| **Hot** | Last contact ≤14 days AND received response | 1.2x boost |
| **Warm** | Last contact ≤14 days without response, OR >14 days with high fit | 1.0x (no boost) |
| **Cold** | Last contact >30 days AND low engagement | 0.9x penalty |

### Eligibility Filtering

Before ranking, filter out ineligible PCRs:

- **Recency Rules:** Exclude if last touch < minimum wait time for any action type
- **Snooze Status:** Exclude if snoozed and snooze period not elapsed
- **Dismiss Cooldown:** Exclude if dismissed within last 30 days
- **Data Completeness:** Require at least one contact method (email, phone, or LinkedIn)

### Always-Show Overrides (Reactions)

These conditions bypass eligibility filtering and elevate PCRs to top of Today List:

| Trigger | Detection | Default Action |
|---------|-----------|----------------|
| Inbound Reply | Email/LinkedIn reply detected | Follow-up within 24h |
| Meeting in 48h | Calendar integration | Meeting prep or post-meeting recap |
| Commitment Due | User-logged commitment date | Reminder to fulfill commitment |
| Triggering Signal | Funding, job change, company news | Congratulations or timely outreach |
| Birthday/Holiday | User-provided or enriched data | Personal note or greeting |

## 2.4 NBA Algorithm (Next Best Action)

For each PCR selected by NBC, the NBA algorithm determines the optimal action type.

### Hard Constraint

> **CRITICAL:** The LLM CANNOT choose the action type. Action type is selected by the deterministic rule engine. The LLM only generates the message content and contextual CTA text after the action type is determined.

### Rule Priority Hierarchy (Draft)

Rules evaluated in order; first matching rule wins:

1. **Always-Show Overrides:** Inbound reply → FOLLOW_UP; Meeting in 48h → RECAP/PREP; Commitment due → FOLLOW_UP; Signal → CONGRATS
2. **Post-Meeting Rules:** Meeting completed <24h AND no recap sent → RECAP
3. **No-Response Rules:** Last action no response AND >14 days → FOLLOW_UP
4. **Signal-Triggered Rules:** Active signal (funding, job change, etc.) → CONGRATS + VALUE_SHARE
5. **Warmth Decay Rules:** Warmth below threshold AND no touch >21 days → CHECK_IN
6. **Cadence Rules:** High fit AND no touch >30 days → VALUE_SHARE; Low fit AND >90 days → RECONNECT
7. **Default:** CHECK_IN (light touch, low risk)

### Template Family Mapping

Each action intent maps to a template family that guides draft generation:

| Action Intent | Template Family | Typical Use Case |
|---------------|-----------------|------------------|
| FOLLOW_UP | POST_MEETING / NO_RESPONSE | Continue conversation after meeting or silence |
| RECAP | POST_MEETING | Summarize meeting, confirm next steps |
| CONGRATS | SIGNAL_TRIGGERED | Acknowledge funding, promotion, award |
| VALUE_SHARE | RELATIONSHIP_BUILDING | Share relevant article, insight, or resource |
| CHECK_IN | WARMTH_DECAY | Light touch to maintain relationship |
| INTRO_REQUEST | INTRODUCTION | Ask for introduction to someone in their network |
| RECONNECT | WARMTH_DECAY | Re-engage after extended silence |

### Contextual CTA Generation

After action type is selected, the LLM generates a contextual CTA that references specific context:

| Bad Examples | Good Examples |
|--------------|---------------|
| "Send Email" | "Send congratulations on Series B funding" |
| "Follow Up" | "Follow up on pricing discussion from last Tuesday" |

CTAs are generated from the Why Payload (structured context) using LLM. May also use predefined templates for common patterns.

## 2.5 Signal System Architecture

### MVP Signal Approach: Just-in-Time Research

Rather than always-on RSS monitoring, MVP uses targeted research for top-ranked contacts:

1. NBC ranks all PCRs using available data
2. Top 3-7 PCRs selected for Today List
3. Automated lookup triggered (Perplexity/Google API) for each selected PCR
4. Signals extracted and attached to PCR context
5. Today List displayed with signal context in Why panel

**Rate limiting:** Lookups are rate-limited by subscription tier to control costs. Higher tiers get more lookups per day.

### Signal Types and TTL

| Signal Type | TTL (Days) | Entity Level | Suggested Action |
|-------------|------------|--------------|------------------|
| Funding Round | 120 | Company | Congrats + value share |
| Hiring Spike | 45 | Company | Intro offer + referral |
| Leadership Change | 60 | Company | Congrats or reconnect |
| Role Change | 30 | Individual | Congrats + reconnect |
| Topic Posting | 14 | Individual | Engage with content |
| Award/Recognition | 30 | Individual | Congrats |
| Inbound Reply | 7 | Behavioral | Immediate follow-up |
| Meeting Scheduled | Until date | Behavioral | Prep before, recap after |
| Birthday | 7 | Individual | Personal greeting |

*TTL (Time-to-Live) determines how long a signal remains actionable. Expired signals are archived but not surfaced.*

## 2.6 Warmth Decay Algorithm

Warmth decays over time based on days since last interaction, with channel-specific weighting.

### Decay Formula

```
warmth_decay = base_decay × (1 + strategic_multiplier)
```

Where:
- `base_decay` = exponential decay from days since last touch
- `strategic_multiplier` = acceleration factor for high-strategic contacts (0.0 - 0.5)

### Channel Weighting

Different interaction types contribute differently to warmth:

| Channel | Warmth Weight | Decay Resistance |
|---------|---------------|------------------|
| In-Person Meeting | 1.0 (strongest) | High |
| Video Call | 0.9 | High |
| Phone Call | 0.8 | Medium |
| Email (with reply) | 0.7 | Medium |
| Email (no reply) | 0.4 | Low |
| LinkedIn Message | 0.5 | Low |
| Text Message | 0.6 | Low |

### Execution

- **Timing:** Nightly batch job via Celery Beat
- **Scope:** All PCRs with lastTouchAt > 0
- **Output:** Updated warmth scores, new score snapshots
- **Telemetry:** Decay events logged for analysis

## 2.7 Confidence Bands

Each score includes a confidence band that affects how aggressively the system recommends actions.

### Confidence Levels

| Level | Conditions | Behavior Impact |
|-------|------------|-----------------|
| **High** | >10 interactions AND <30 days stale AND complete ICP data | Full range of actions available; aggressive asks permitted |
| **Medium** | 3-10 interactions AND <90 days stale | Standard actions; moderate asks |
| **Low** | <3 interactions OR >90 days stale OR missing ICP fields | Light-touch only; no aggressive asks; prompt for missing data |

### Low Confidence Behavior

When confidence is Low, the system:
- Suggests only light-touch actions (value share, congrats, check-in)
- Avoids aggressive asks (intro requests, direct pitches)
- Prompts user for missing context (non-blocking)
- Displays "needs more data" indicator in UI

## 2.8 Cold Start Strategy

For new users without engagement history, the system initializes scores using:

### Approach: Pairwise Comparisons

During onboarding, users complete 5+ pairwise comparisons between mock client profiles:

1. Present two hypothetical clients of roughly equal value
2. User chooses which they'd prefer to work with
3. System infers dimension preferences from choices
4. Initial weights derived using log-loss optimization
5. Option to do more comparisons for higher accuracy

### Alternative: CRM Import Analysis

If user imports CRM data with historical outcomes:

1. Analyze past clients to derive ICP patterns
2. Extract dimension values from successful relationships
3. Initialize weights based on what correlated with success

### Hybrid Approach

If user provides both CRM data AND completes comparisons:
- CRM data provides baseline weights
- Pairwise comparisons fill gaps and add personal preference signal
- CRM overrides where data exists; comparisons fill gaps

*Note: We explicitly chose NOT to use KNN over a professional panel. Pairwise comparisons provide more direct signal about user preferences without the complexity of building/maintaining a reference panel.*

## 2.9 Future Vision: Post-MVP Algorithms

The following capabilities are planned for future releases, building on the MVP foundation.

### Random Forest + RuleFit Scoring

The MVP additive distance model will evolve into a more sophisticated ensemble:

- **Random Forest:** Captures non-linear relationships between dimensions
- **RuleFit:** Extracts interpretable rules from the forest for explainability
- **Per-tree weight learning:** Individual trees can be up/down-weighted based on user feedback

### 4-Component NBA System

The rule-based NBA will evolve to consider four components:

1. **Fit:** ICP match (from scoring)
2. **Engagement:** Historical response patterns for this action type
3. **Freshness:** Time since last touch and action variety
4. **Effort:** Cost/time required for each action type

### Multi-Armed Bandit for Experimentation

A/B testing with automatic traffic allocation to winning variants:

- Test subject lines, cadences, action recommendations
- Thompson Sampling or UCB for exploration/exploitation balance
- Auto-promote winners without manual intervention

### Always-On Signal Monitoring

Replace JIT research with continuous monitoring:

- RSS feeds for company news and funding announcements
- LinkedIn activity tracking (with proper permissions)
- Entity linking to automatically connect signals to PCRs
- Change detection to surface only what's new

### Attribution & ROI Modeling

Connect actions to business outcomes:

- Multi-touch attribution models
- Revenue attribution per action type and PCR
- Churn prediction for existing client relationships
- Cohort analysis for relationship lifecycle

### Conversion Probability Prediction (Feature #20)

Show users a dynamic probability of converting each prospect within a configurable timeframe.

**Problem Solved:** Users can see *who* to contact and *what* action to take, but lack visibility into the *likelihood* of success. This feature adds predictive intelligence to help prioritize high-probability opportunities.

**Algorithm:**

```
P(conversion | timeframe) = σ(β₀ + β₁·fit + β₂·warmth + β₃·actions + β₄·signals + β₅·similarity)

Where:
- σ = sigmoid function (outputs 0-100%)
- β weights learned from historical conversion outcomes
- similarity = weighted average of conversion rates for similar prospects
```

**Key Design Decisions:**

| Decision | Choice | Rationale |
|----------|--------|-----------|
| Visual Display | Meter/Gauge | Simple, universally understood progress bar |
| Default Timeframe | 90 days | More stable predictions with sufficient data window |
| NBC Integration | Informational Only | Keep separate from ICP-based ranking |
| PCR Scope | Prospects Only | Focus on conversion-relevant contacts first |

**Display Mockup:**

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
└─────────────────────────────────────────┘
```

**Prerequisites:**
1. Outcome Tracking must be implemented first (user marks prospects as Converted/Lost)
2. 3+ months of outcome data needed for statistical significance
3. Model retraining runs weekly with new outcomes

**Phased Rollout:**
- Phase 0 (MVP+1 month): Implement Outcome Tracking, begin data collection
- Phase 1 (MVP+3 months): Launch basic probability display
- Phase 2 (MVP+4 months): Add timeframe selection, trend indicators
- Phase 3 (MVP+6 months): Per-user model personalization

**Full PRD:** See [PRD-001-Conversion-Probability-Prediction.md](docs/PRD/PRD-001-Conversion-Probability-Prediction.md)

---

## Document Information

| Field | Value |
|-------|-------|
| **Version** | 2.1 |
| **Date** | January 2026 |
| **Status** | Draft - Updated with Conversion Probability Feature |
| **Authors** | TractionBD Product & Engineering Team |
| **Related Documents** | 01-functional-requirements.md, 02-expanded-prd.md, 03-data-model.md, 04-work-breakdown.md, 05-estimates.md, docs/PRD/PRD-001-Conversion-Probability-Prediction.md |

## Change Log

| Version | Date | Changes |
|---------|------|---------|
| 1.0 | November 2025 | Initial 19-feature catalog |
| 2.0 | January 2026 | Major update: NBC/NBA architecture, TBDA-59/60 scoring, PCR type weighting, recency rules, temperature model, JIT research, confidence bands, cold start approach, MVP vs Future delineation |
| 2.1 | January 2026 | Added Feature #20: Conversion Probability Prediction with full technical spec and PRD reference |
