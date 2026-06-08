# Q3 LLM ESG Message Triage Experiment Log

## Experiment Title

Using a Large Language Model for ESG Message Triage

## Assessment Context

This experiment supports Question 3 of the assessment. The task was to prototype an automation system that uses a Large Language Model to classify and triage ESG-related operational messages in real time. The system should extract the issue category, urgency, sentiment, follow-up requirement, recommended team, escalation reason, data sensitivity risk, summary, confidence and human review requirement.

## Tool Used

- ChatGPT was used as the API-based Large Language Model.
- A simple keyword-based rule classifier was used as the baseline comparison method.

## Date of Experiment

June 2026

## Aim of Experiment

The aim was to test whether an LLM could accurately classify ESG-related operational messages and return structured JSON. The experiment also compared the LLM output against a simple rule-based baseline to evaluate consistency, errors and limitations.

---

## Revised Prompt Used

```text
You are an ESG operations triage assistant for a large organisation.

Classify each employee message using only the categories and teams listed below.

Allowed issue_category values:
WATER_LEAK, WASTE_CONTAMINATION, ENERGY_WASTE, PROCUREMENT_POLICY, ACCESSIBILITY_BARRIER, GOVERNANCE, OTHER

Allowed urgency values:
LOW, MEDIUM, HIGH, CRITICAL

Urgency rules:
- CRITICAL: immediate danger, flooding, hazardous waste, emergency access blocked.
- HIGH: issue has continued for hours/days, affects safety, accessibility, compliance or major resource waste.
- MEDIUM: operational issue needing follow-up but no immediate risk.
- LOW: general information request or minor concern.

Allowed recommended_team values:
Facilities Team, Sustainability Team, Waste Management Team, Procurement Team, Accessibility and Inclusion Team, Governance and Compliance Team, Safety Team

Return JSON only. Do not invent facts. If information is missing, use "unknown". Include:
issue_category, urgency, sentiment, followup_required, recommended_team, escalation_reason, data_sensitivity_risk, brief_summary, confidence, human_review_required.

Messages:
1. There is a water leak in Building C that has been running all morning.
2. The recycling bins are contaminated again and no one seems to be checking them.
3. The accessible entrance near the main building has been blocked for two days.
```

---

## Why the Prompt Was Improved

The original prompt was useful because it asked the model to extract structured information and return JSON. However, it was too broad because it did not define fixed categories, urgency rules, approved team names or human review requirements.

The revised prompt improved the original version in the following ways:

| Prompt enhancement | Reason for adding it |
|---|---|
| Fixed issue categories | Prevents the model from inventing unsupported labels |
| Fixed urgency values | Improves consistency in escalation decisions |
| Urgency rules | Helps the model distinguish low, medium, high and critical issues |
| Approved team names | Ensures output can be routed to real organisational teams |
| “Do not invent facts” instruction | Reduces hallucination risk |
| Confidence field | Supports later review and quality control |
| Human review field | Ensures high-risk issues are not handled automatically |

---

## Test Messages

| Message number | Test message |
|---|---|
| 1 | There is a water leak in Building C that has been running all morning. |
| 2 | The recycling bins are contaminated again and no one seems to be checking them. |
| 3 | The accessible entrance near the main building has been blocked for two days. |

---

## Final LLM JSON Output

```json
[
  {
    "issue_category": "WATER_LEAK",
    "urgency": "HIGH",
    "sentiment": "NEGATIVE",
    "followup_required": "Y",
    "recommended_team": "Facilities Team",
    "escalation_reason": "The leak has been running all morning, which may cause water waste, property damage or a safety risk.",
    "data_sensitivity_risk": "LOW",
    "brief_summary": "A water leak has been reported in Building C and has continued all morning.",
    "confidence": "HIGH",
    "human_review_required": "Y"
  },
  {
    "issue_category": "WASTE_CONTAMINATION",
    "urgency": "MEDIUM",
    "sentiment": "NEGATIVE",
    "followup_required": "Y",
    "recommended_team": "Waste Management Team",
    "escalation_reason": "Repeated recycling contamination requires follow-up to prevent ongoing waste management and ESG reporting issues.",
    "data_sensitivity_risk": "LOW",
    "brief_summary": "Recycling bins are repeatedly contaminated and may not be monitored properly.",
    "confidence": "HIGH",
    "human_review_required": "N"
  },
  {
    "issue_category": "ACCESSIBILITY_BARRIER",
    "urgency": "HIGH",
    "sentiment": "NEGATIVE",
    "followup_required": "Y",
    "recommended_team": "Accessibility and Inclusion Team",
    "escalation_reason": "The accessible entrance has been blocked for two days, affecting inclusive access and potentially creating compliance risk.",
    "data_sensitivity_risk": "MEDIUM",
    "brief_summary": "The accessible entrance near the main building has been blocked for two days.",
    "confidence": "HIGH",
    "human_review_required": "Y"
  }
]
```

---

## Rule-Based Baseline

A simple rule-based baseline was created using keyword matching.

| Keyword rule | Baseline category | Baseline urgency | Recommended team |
|---|---|---|---|
| Contains “water”, “leak” or “running all morning” | WATER_LEAK | HIGH | Facilities Team |
| Contains “recycling”, “bins” or “contaminated” | WASTE_CONTAMINATION | MEDIUM | Waste Management Team |
| Contains “accessible entrance”, “blocked” or “two days” | ACCESSIBILITY_BARRIER | HIGH | Accessibility and Inclusion Team |
| Contains “air conditioning”, “lights”, “overnight” or “empty office” | ENERGY_WASTE | MEDIUM | Sustainability Team |
| Contains “supplier” or “sustainability policy” | PROCUREMENT_POLICY | MEDIUM | Procurement Team |

---

## Baseline Output

| Test message | Rule-based category | Rule-based urgency | Rule-based team |
|---|---|---|---|
| There is a water leak in Building C that has been running all morning. | WATER_LEAK | HIGH | Facilities Team |
| The recycling bins are contaminated again and no one seems to be checking them. | WASTE_CONTAMINATION | MEDIUM | Waste Management Team |
| The accessible entrance near the main building has been blocked for two days. | ACCESSIBILITY_BARRIER | HIGH | Accessibility and Inclusion Team |

---

## LLM vs Rule-Based Baseline Comparison

| Message | LLM classification | Rule-based baseline | Match? | Observation |
|---|---|---|---|---|
| Water leak in Building C | WATER_LEAK / HIGH | WATER_LEAK / HIGH | Yes | LLM gave a stronger escalation reason and identified possible safety risk. |
| Recycling bins contaminated again | WASTE_CONTAMINATION / MEDIUM | WASTE_CONTAMINATION / MEDIUM | Yes | LLM recognised that the word “again” suggests a repeated issue. |
| Accessible entrance blocked | ACCESSIBILITY_BARRIER / HIGH | ACCESSIBILITY_BARRIER / HIGH | Yes | LLM correctly identified accessibility impact and required human review. |

---

## Error Analysis

The LLM performed well on the selected examples, but there are situations where it may perform worse than a rule-based baseline.

For example, if a user writes:

```text
The bins are a disaster and nobody cares.
```

The LLM may over-escalate the issue because of the emotional tone. A rule-based baseline would classify it more predictably as WASTE_CONTAMINATION.

Another example is:

```text
The office feels unsafe because the lights are off.
```

The LLM may classify this as a safety issue because of the word “unsafe”, while a rule-based system might classify it as ENERGY_WASTE or a facilities issue depending on the keyword rules.

This shows that the LLM is more flexible, but it may also be less predictable than simple rules.

---

## Bias Analysis

Bias may occur if the model treats strongly worded messages as more urgent than calmly written messages, even when the operational risk is the same. It may also misclassify messages written by non-native English speakers, staff using short phrases, informal wording, spelling mistakes or accessibility-related language.

To reduce bias, the system should be tested with a wider dataset that includes:

- different writing styles
- short and long messages
- spelling errors
- non-native English phrasing
- multilingual examples
- accessibility-related reports
- ambiguous operational messages

---

## Structured Output Validation

In a production version, the JSON output should be validated before storage or routing. A validation layer should check that:

- all required fields are present
- `issue_category` matches the approved list
- `urgency` is only LOW, MEDIUM, HIGH or CRITICAL
- `recommended_team` matches the approved list
- the JSON format is valid
- high-risk or low-confidence outputs are flagged for human review

If the JSON output is incomplete, invalid or unsupported, the message should not be automatically escalated. It should be placed in a human review queue.

---

## Confidence Score Strategy

| Confidence or risk level | Recommended action |
|---|---|
| HIGH confidence and LOW/MEDIUM urgency | Automatically route to the relevant team |
| MEDIUM confidence | Send to review before routing |
| LOW confidence | Human review required |
| HIGH or CRITICAL urgency | Human review required |
| Safety, accessibility, procurement or governance issue | Human review required |

---

## Human-in-the-Loop Workflow

The recommended workflow is:

1. Employee submits an ESG-related operational message.
2. LLM generates structured JSON classification.
3. Validation layer checks the JSON output.
4. High-risk, low-confidence or sensitive cases are sent to human review.
5. Human reviewer confirms or corrects the classification.
6. The final decision is stored and routed to the relevant team.
7. Corrections are logged to improve future prompt rules and baseline rules.

---

## Operational Monitoring

The system should monitor:

- classification accuracy
- false escalation rate
- missed urgent cases
- invalid JSON outputs
- human override rate
- processing time
- repeated classification errors
- model drift over time

Regular audits should compare LLM decisions with final human decisions.

---

## Experiment Reflection

The LLM was useful because it returned structured JSON, produced clear summaries and gave better escalation reasons than the rule-based baseline. The rule-based baseline was more predictable but less flexible. The experiment shows that LLM-based ESG triage is suitable for a limited supervised pilot, but not for unsupervised production use.

The main risks are hallucination, bias, over-escalation, under-escalation, invalid JSON output and privacy exposure. A production version would require structured validation, confidence thresholds, human review, audit logs and ongoing monitoring.
