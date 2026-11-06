---
layout: page
title: Contingent Workforce Supplier Review
subtitle: A contractor data and supplier reporting tool built with AI
---

[← Back to portfolio home](/)

**[JavaScript · SheetJS · SVG charts · Claude]**

[Open the tool](/assets/css/contingent-workforce-supplier-review.html) · <a href="/assets/css/contingent-workforce-supplier-review.html" download>Download it</a> (single HTML file, opens in Chrome or Edge, no install)

![Governance trends for a fictional clinical supply chain contractor workforce](/assets/css/cwsr-01-governance.png)

## Why I built this

I read a job description for a resource strategy and analytics role in a clinical supply chain group. Much of it was familiar from my people analytics work, especially headcount reporting and reconciling data across workforce systems. The part that was new to me was the supplier side: the staffing agencies that provide contractors, and the rates and purchase orders behind them.

I built this tool to learn what that work involves by making a small working version of it. I'm not a procurement or contingent workforce specialist, and the tool reflects what I learned while building it, with Claude acting as both a guide to the domain and a developer.

## The problem, as I came to understand it

A company that uses a lot of contractors usually has their data spread across several systems. A vendor management system (VMS) tracks assignments and bill rates. Procurement holds the rate cards and purchase orders. Finance records what was actually paid. Workday may hold its own contingent worker records. Each system uses its own IDs and column names, and each has gaps.

That leads to a few recurring problems:

- **Counts disagree.** The VMS, Workday and Finance can each report a different number of active contractors, and a meeting ends up arguing about which number is right.
- **Errors hide in the details.** A contractor billed above the contracted rate, or invoices that continue after an assignment ended, are easy to miss across thousands of spend lines.
- **Budgets run out quietly.** A purchase order can run out of money before the work it funds is finished, and nobody notices until invoices are rejected.
- **Supplier comparisons are hard.** When the same type of work is spread across many agencies at different rates, it's hard to see without first stitching the data together.

## What the tool does

Contingent Workforce Supplier Review is a single HTML file. An analyst loads exports from those systems, the tool checks and matches them, and it produces monthly reporting and a supplier comparison. Everything runs in the browser, so no data leaves the computer. The demo uses fictional data for a clinical supply chain group with 8 staffing suppliers and about 140 contractors.

### Getting the data right

- **Load and map.** Any number of Excel or CSV files can be loaded. The tool guesses which column holds which field, the analyst confirms, and the mapping is saved so next month's refresh is faster. The demo includes two roster files in different layouts to show this.
- **Data check.** Before any reporting, the tool lists problems and asks the analyst to decide on the ones that need judgment. In the demo it first asks whether two spellings of Apex Staffing are the same supplier and which role family 7 contractors belong to. Once those are settled, it shows 6 contractors billed above their contracted rate (about $81,000 a year), 4 still billing after their assignment ended, and 5 spend lines with no purchase order. Nothing is merged or dropped automatically, and every decision goes into a log that can be undone.

![Data check with supplier name merges and contractors missing a role family](/assets/css/cwsr-02-data-check.png)

- **Definitions and reconciliation.** A short data dictionary states how each number is defined, and a bridge chart explains why the roster shows 142 active assignments while Workday shows 128 active contractors. The gap breaks down into 3 duplicate assignments, 5 contractors missing from Workday, 9 who have ended in Workday but are still active in the roster, and 3 the other way around. Clicking a bar lists the people behind it.

![Bridge chart from 142 roster assignments to 128 active Workday records](/assets/css/cwsr-03-bridge.png)

### Monthly reporting

- **Governance.** Four measures are tracked each month against targets: rate compliance, off-contract spend, contractors past a tenure limit, and data completeness. In the demo, off-contract spend rises from about 6% to 11.6% after a trial ramp-up in August, which is marked on the chart as an event. The tenure limit and targets are settings rather than fixed values, since each company sets its own.
- **PO burn.** For each purchase order, the tool compares how much of the money is spent with how much of its time has passed. In the demo, one PO is 91% spent with 83% of the year gone and is projected to run out around December 4, while four of its contractors are booked into next year. A multi-year PO is already over its 2026 budget, and two POs are spending slowly enough that Finance might be able to release some of the money.

![PO burn chart comparing budget used with time elapsed](/assets/css/cwsr-04-po-burn.png)

- **Distribution.** Contractors are grouped by study, cost center and the reason the role exists (tied to a trial, a project, a backfill, or ongoing work). In the demo, 91 of 139 contractors are tied to trials. Four contractors in ongoing roles have been there more than 24 months, so the tool lists them as roles to review for conversion to employees.

![Contractors by study and demand driver](/assets/css/cwsr-09-distribution.png)

### Comparing suppliers

- **Spend map.** A grid shows each supplier's average rate, headcount and spend in each role family, colored by how far the rate sits from the median for that role family. In the demo, clinical data coordination is split across 6 suppliers at $58 to $74 an hour, while IRT and systems work relies on a single supplier.

![Supplier by role family grid with rates compared to the internal median](/assets/css/cwsr-05-spend-map.png)

- **Scorecard.** Within each role family, suppliers are compared on rate, time to fill, 90-day retention, conversion to employee, and manager rating. Each measure is scored relative to the other suppliers, and sliders set how much each one counts. Changing the weights shows whether a ranking holds up: in the demo, giving time to fill more weight moves a fast but expensive supplier ahead of a slower, cheaper one. Measures with no data are left out rather than counted as zero, and suppliers with fewer than 3 contractors are marked low confidence.

![Supplier scorecard with adjustable weights](/assets/css/cwsr-06-scorecard.png)

### Sharing the result

- **Briefing.** Any page can be turned into a 16:9 slide that states the finding above its chart and key numbers. The slides can be put in order and exported as one PDF, and detail tables export to Excel.

![Briefing page with four cards collected](/assets/css/cwsr-07-briefing.png)

![A briefing card on purchase order burn](/assets/css/cwsr-08-briefing-card.png)

## How I think about it

**It reports; it doesn't decide.** The tool shows where the data disagrees and how suppliers compare. Decisions about contracts and suppliers belong to the people who know the business and the supplier relationships.

**Every number can be traced.** Clicking a figure shows the contractors or spend lines behind it, down to the file and row they came from.

**Bad data should be visible.** A blank rate, a missing end date or an expired rate card is flagged and left out of the calculations, instead of quietly becoming a zero.

**Assumptions are settings.** Tenure limit, targets, fiscal year start and hours per week can all be changed, and the definitions page shows the values in use.

**There is no AI inside the tool.** The same inputs always produce the same answer. AI helped me design and build it, and a separate AI agent reviewed it.

## What I left out, and why

I also designed scenario pages: consolidating suppliers, a supplier exiting, a rate increase at renewal, and a roadmap built from the results. I removed them from this version. They depend on things I don't yet understand well enough to defend, such as the real cost of moving contractors between agencies and what supplier contracts allow. I'd rather show a smaller tool I can explain than a larger one I can't.

Other gaps I know about:

- Rates are compared only with other suppliers in the same data, not with market benchmarks.
- The tool assumes one currency.
- Role families, tenure limits and targets vary between companies. The demo values are reasonable placeholders, not standards.

## How I built it

1. **Start from the job description.** I listed what the role is responsible for and asked which parts a small tool could show.
2. **Learn the domain before designing.** I asked basic questions first, such as why contractor spend changes in a clinical supply chain and how purchase orders work across fiscal years.
3. **Write the design before the code.** A short spec set the purpose, the pages and the data each page needs, along with what was out of scope.
4. **Test with messy, fictional data.** The sample data has problems planted on purpose. A separate script, written independently of the tool, calculates the expected answers, and the tool is tested against them.
5. **Have a separate AI agent review the build.** The review found 16 issues, mainly blank values being treated as zeros and expired rate cards still being used. All were fixed and turned into tests.
6. **Cut what I couldn't explain.** The scenario pages came out for the reason above.

If an interviewer asks how I'd take this further, my honest answer is that I'd start by talking with people who do this work, to learn how they define role families and what they would need to see before trusting a number like rate compliance.

## Try it for yourself

[Open Contingent Workforce Supplier Review](/assets/css/contingent-workforce-supplier-review.html) in Chrome or Edge, or <a href="/assets/css/contingent-workforce-supplier-review.html" download>download the file</a> to run it from your desktop. The demo data loads automatically, and the Data check page is the place to start. You can also <a href="/assets/css/supplier-review-sample-data.xlsx" download>download the sample workbook</a> to practice loading files. All suppliers, people, rates and amounts in the sample are fictional.

[← Back to portfolio home](/)
