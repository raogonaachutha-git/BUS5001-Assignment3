# Q3 LLM ESG Message Triage Experiment Log

## Assessment Question

Q3 required a prototype automation system using a Large Language Model to classify and triage ESG-related operational messages in real time. The system needed to extract issue category, urgency, follow-up requirement and recommended team or escalation pathway.

## Tool Used

ChatGPT was used as an API-based LLM for the main classification test. A simple rule-based keyword baseline was also created for comparison.

## Date of Experiment

June 2026

## Objective

The objective was to test whether an LLM could classify ESG-related operational messages into structured JSON and compare the results against a rule-based baseline.

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