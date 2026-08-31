# Week 2, Task 2: Prompt Ladder

**Scenario:** I have a CSV of customer churn data (columns: `customer_id, tenure_months, monthly_charges, total_charges, support_tickets, churn`) and I ask an AI to help me understand it.

## Version 0

**Prompt:** `Explain this data.`

**Output:** This dataset appears to contain customer information, possibly related to a subscription service. It has columns for tenure, charges, and churn status. Churn typically refers to customers leaving. To analyze this properly, you could look at correlations between tenure and churn, or use a classification model like logistic regression. Let me know if you'd like help with that.

**Problem:** Doesn't touch the actual data. Guesses at column meaning instead of using it. No actionable insight.

## Version 1

**Prompt:** `Explain this data. I need to figure out *why* customers are churning so I can present findings to my product team.`

**Output:** Churn is likely driven by a combination of factors visible in your columns, high monthly charges relative to usage, and support ticket volume are common churn predictors. To find the actual drivers, you'd want to compare average values of each column between churned and retained customers.

- **Prompt change:** Added a purpose (diagnose churn drivers) instead of open-ended "explain."
- **Output improvement:** Shifted from a dictionary-style summary to a hypothesis-driven angle — it now names *candidate churn drivers* instead of just defining terms.
- **Still failed:** Still speaking in generalities ("likely," "common predictors") because it has no actual numbers from my data — it's guessing based on domain patterns, not my dataset.
- **Try next:** Give it real data/stats so it stops guessing.

## Version 2

**Prompt:** `Explain this data. I need to figure out why customers are churning so I can present findings to my product team. My audience is non-technical product managers with no stats background.`

**Output:** Churn is likely tied to a few key factors your product managers will care about: how long customers stick around before leaving, how much they're paying, and how often they contact support. Avoiding technical terms like "logistic regression," the story to tell is: customers who churn tend to leave earlier and complain more.

- **Prompt change:** Added an audience description.
- **Output improvement:** Dropped stats jargon ("logistic regression," "correlation") — reads more like a memo than a data-science note.
- **Still failed (honest "didn't help" moment):** The *substance* didn't change at all — still the same vague, ungrounded guesses as V1, just reworded in plainer language. Audience framing polished tone, not accuracy. This is the layer that gave the least return so far.
- **Try next:** Stop tuning tone and give it the actual data — that's the real bottleneck, not vocabulary.

## Version 3 — Added: Real Context (Actual Data)

**Prompt:** `Explain this data. I need to figure out why customers are churning so I can present findings to my product team. My audience is non-technical product managers with no stats background. Here is a summary of the data: [500 rows; churned customers avg tenure = 4.2 months vs 18.6 months for retained; churned avg monthly_charges = $84 vs $61; churned avg support_tickets = 3.1 vs 0.8].`

**Output:** Your data shows three clear churn signals: (1) Churned customers leave fast — average tenure of 4.2 months vs. 18.6 for retained customers. (2) They're paying more — $84/month vs $61. (3) They contact support far more — 3.1 tickets vs 0.8. This points to a pricing/value mismatch for new customers, likely worsened by poor early support experience.

- **Prompt change:** Supplied real summary statistics instead of letting the model guess.
- **Output improvement:** The model now cites *my actual numbers* and draws a specific, testable conclusion (pricing/value mismatch + early support friction) instead of generic churn theory.
- **Still failed:** No output structure yet. For a PM presentation, this needs to be scannable, not read as prose.
- **Try next:** Specify an output format for presentation use.

## Version 4

**Prompt:** *(V3 prompt) +* `Format the answer as: a 3-bullet "Key Findings" section (one line each), followed by a 1-line "Recommended Next Step" for each finding.`

**Output:**
**Key Findings**
- Churned customers leave 4x faster (4.2 vs 18.6 months tenure).
- Churners pay 38% more per month ($84 vs $61).
- Churners file 4x more support tickets (3.1 vs 0.8).

**Recommended Next Steps**
- Investigate early-tenure onboarding to reduce first-90-day drop-off.
- Review pricing tiers for new vs. long-term customers.
- Audit support ticket resolution time/quality for at-risk accounts.

**Prompt change:** Specified exact structure (bullets + paired action per finding).
- **Output improvement:** Directly presentation-ready — no editing needed before pasting into slides. Each finding is now paired with an action, which V3's prose format didn't offer.
- **Still failed:** Findings are still just restated stats — no quantified business impact ("this costs us $X/month") and no caveat that these are correlations, not proven causes.
- **Try next:** Add constraints to force rigor (quantify impact, flag correlation vs. causation).


## Version 5

**Prompt:** `Constraints: (1) explicitly label these as correlations, not proven causes, (2) do not recommend any action you can't tie back to one of the three stats given, (3) keep the whole answer under 120 words.`

**Output:**
**Key Findings** *(correlations, not confirmed causes)*
- Early churners average 4.2 months tenure vs. 18.6 for retained — early-stage drop-off is the biggest gap.
- Churners pay $84/mo vs $61 — price sensitivity is plausible but unproven.
- Churners log 3.1 support tickets vs 0.8 — support friction correlates strongly with churn.

**Recommended Next Steps**
- Test an improved onboarding flow for the first 90 days.
- A/B test pricing for new accounts before assuming price is the cause.
- Prioritize support response time for high-ticket accounts.

**Prompt change:** Added explicit constraints on rigor and length.
- **Output improvement:** Now epistemically honest (correlation vs. causation flagged) and every recommendation is traceable to a specific stat — no unsupported suggestions slipped in.
- **Still failed:** Constraints made it terser but also stripped some readability — under 120 words meant it removed brief connecting phrases that helped the narrative flow for a non-technical reader.
- **Try next:** If iterating further, add an example of an ideal past answer (a "good look like" sample) to balance rigor with narrative flow.

## Summary of What Earned Its Place

| Layer | Payoff |
|---|---|
| Goal | Moderate — gave direction |
| Audience | **Low** — cosmetic tone shift only, no substance change |
| Real context (data) | **Highest** — turned guesses into grounded findings |
| Output format | High — made it usable without editing |
| Constraints | Moderate-high — added rigor, cost some flow |

## Final Reusable Prompt

```
Explain this data. Goal: identify why customers are churning so I can present findings to my product team.
Audience: non-technical product managers, no stats background — avoid jargon.

Data summary: [paste key stats/comparisons between churned vs. retained customers here]

Format the answer as:
- "Key Findings": 3 bullets, one stat-backed observation each, labeled as correlations not proven causes
- "Recommended Next Steps": one action per finding, only if directly traceable to a stat given

Constraints: under 150 words total; do not include any claim not backed by the data provided.