---
layout: page
title: FDA Compliance Data Pipeline
subtitle: An Alteryx workflow that turns four public FDA datasets into a validated, Tableau-ready model of inspections, citations, enforcement actions and recalls.
---

[← Back to home](/)

## The Problem

FDA publishes a lot of compliance data, but it arrives as four separate exports with different grains, inconsistent fields and no shared event key. Inspection outcomes sit in one file, Form 483 observations in another, Warning Letters in a third and product recalls in a fourth. Every field loads as text, dates are stored as strings, placeholders like "-" stand in for blanks, and some free-text fields say the opposite of what they mean. A question like *"Which regulations are most often cited at drug and device sites that end up with an Official Action Indicated outcome?"* can't be answered from the dashboard without first cleaning and connecting the data.

## The Solution

I built an Alteryx Designer workflow that sources about 645,000 records from the FDA Data Dashboard, cleans and standardizes each dataset, reconciles the results to FDA's own published counts, and models the output as four fact tables sharing one facility dimension. The scope is drug and device manufacturers from FY2022 to date, and the final tables are written out as Tableau Hyper extracts.

## How It Works

**1. Data sourcing (FDA Data Dashboard)**
- Pulled four datasets: inspection classifications (343,456 rows), Form 483 citations (281,873), compliance actions (1,309) and recalls (18,746).
- The filtered exports for Inspections and Citations stalled on the dashboard, so I downloaded the full files and applied the Drugs/Devices and FY2022+ scope inside Alteryx.

**2. Exploration before transformation**
- Profiled every field with Browse and Summarize tools, and documented findings in a Comment inside each container before changing anything.
- Found that all fields loaded as `V_WString` at about 1 billion characters, that Inspections has one row per inspection *per project area*, and that Citations has no year or product field.

**3. Cleaning (one container per dataset)**
- **Inspections:** right-sized every field type, filtered to scope (19,980 rows kept), parsed dates and built `Classification_Code` (NAI / VAI / OAI), a `Has_Citations` flag and `Days_To_FMD145` (FDA's turnaround from inspection close to classification letter).
- **Citations:** extracted the CFR part from text like "21 CFR 211.192" with regex and mapped it to a `Regulation_Area` (Drug cGMP, Device Quality System and others), covering 88% of citations. Mapped Radiologic Health to Devices, then joined to cleaned Inspections on `Inspection_ID` + product type so citations inherit the year and product scope.
- **Compliance Actions:** derived FDA's October-start fiscal year and identified `Case_ID` as the true action key after testing two hypotheses (details below).
- **Recalls:** built `Event_Severity` (Class I to III), a `Distribution_Scope` field that corrects misleading "Nationwide" text, and a rule-based `Recall_Reason_Category` classifier with 13 root-cause categories.

**4. Modeling and output**
- Unioned facility records from all four sources, ranked them by completeness and deduplicated to one row per `FEI_Number` (FDA's facility ID).
- The result is a galaxy schema: four fact tables (Inspections, Citations, Compliance Actions, Recalls) sharing a conformed Firm dimension, with Citations also linked to Inspections by `Inspection_ID`.
- Wrote five `.hyper` extracts for Tableau.

## Example Checks and Outputs

**Check 1: Do my cleaned counts match FDA's own numbers?**

A Cross Tab of cleaned inspections by fiscal year and classification reproduced the FDA dashboard's figures for Drugs and Devices exactly (for example, FY2022: 1,706 NAI, 1,403 VAI, 215 OAI).

**Output:**

| Fiscal Year | NAI | VAI | OAI | OAI rate |
|---|---|---|---|---|
| 2022 | 1,706 | 1,403 | 215 | 6.5% |
| 2023 | 2,222 | 1,798 | 295 | 6.8% |
| 2024 | 2,124 | 1,821 | 304 | 7.2% |
| 2025 | 2,434 | 2,008 | 378 | 7.8% |
| 2026 | 1,840 | 1,263 | 169 | 5.2% |

The OAI rate rose each year from FY2022 to FY2025. FY2026 looks lower, likely because recent inspections are still waiting for a final classification.

**Check 2: Why does the compliance file have more rows than FDA reports?**

The compliance export had 1,309 rows, but the dashboard showed 1,231 actions. My first hypothesis, one letter listed once per product type, explained only 7 rows. Testing uniqueness on `Case_ID` returned exactly 1,231, which showed that single Warning Letters often name several facilities.

**Output:**

| Test | Unique rows | Gap explained |
|---|---|---|
| Firm + date + action type | 1,302 | 7 of 78 rows |
| `Case_ID` alone | 1,231 | All 78 rows |

I kept one row per action per facility, so every site links to its inspections and recalls, and documented `COUNTD(Case_ID)` as the correct way to count actions.

## Results

- **Validated against the source:** inspection counts and compliance action counts both reconcile to FDA's published figures, so the cleaned model can be trusted for trend reporting.
- **Corrected misleading text:** recalls described as "US Nationwide distribution in the state of MS" were being read as national. State-list detection reclassified about 3,000 recalled products, giving a realistic split of 59% nationwide and 16% limited to specific states.
- **Turned free text into analysis-ready categories:** CFR extraction mapped 88% of 18,534 in-scope citations to a regulation area, and the recall classifier was refined in two rounds against real examples to cut the "Other" share.
- **An early finding:** 31% of in-scope inspections (4,878 of 15,789) produced at least one posted Form 483 citation.

## What I Learned

This was my first Alteryx workflow, and the biggest lesson was that most of the work happens before any transformation. Profiling each file first showed me the grain of every dataset, and that's what kept the joins from silently duplicating rows. Reconciling to FDA's published numbers turned out to be the most valuable step: the 78-row gap in compliance actions looked like a data error until I tested it, and it turned out to be a real feature of how FDA issues letters. I also learned that rule-based text classification gets you far, but the order of the rules is a design decision. Putting root causes like material degradation ahead of symptoms like fracture changed the results. Building this also prepared me for the Alteryx Designer Core certification, which I passed.

## Tech Stack

Alteryx Designer · Tableau (Hyper extracts) · Regex · FDA Data Dashboard · Dimensional modeling

## What's Next

The next step is a Tableau dashboard that follows the escalation chain from inspection outcome to Warning Letter to recall, at the facility level. After that, I want to replace the rule-based recall classifier with an LLM-based classifier validated against a hand-labeled sample, and explore a graph model of which CFR deficiencies tend to be cited together.

- [GitHub](https://github.com/l-batista)
- [LinkedIn](https://www.linkedin.com/in/luisgbatista/)
