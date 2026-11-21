---
layout: page
title: Scaled Slide Automation for Sales Rep Reviews
subtitle: Turning a hand-built, biweekly 200-slide process into a one-click run
---

[← Back to home](/)

*This page covers the business project. For how the underlying tool works, see the [Slide Automation Tool](/slide-automation-tool/).*

## The Problem

A colleague on another team heard about a slide tool I had built for my own reporting and reached out. Their team was producing rep overview slides entirely by hand: two slides for each of roughly 100 sales reps, every other week.

Each run meant opening an Excel file, copying numbers one by one, and pasting them into PowerPoint to rebuild about 200 slides. The work was slow and repetitive, and every copied number was a chance for an error to reach a sales leader.

## The Solution

I adapted my existing slide tool to fit their process. The original version filled one PowerPoint template from one data file. I reconfigured it to work with multiple templates, so it could produce every slide for every rep in a single run.

The team now sets up their slide templates once. Each cycle, they update the Excel file with the latest numbers and generate all 200 slides at the same time.

## How It Works

**1. Templates set up once**
- The team's existing rep slides became templates, with placeholders where the numbers go.
- Both slide designs live side by side, so each rep gets both of their slides from the same run.

**2. Data updated each cycle**
- The only recurring step is refreshing the Excel file with the current period's numbers.
- Each row maps to a rep, and each column maps to a placeholder in the templates.

**3. One run for all reps**
- The tool reads the file, fills every template for every rep, and outputs the finished slides together.
- Number formats carry over from the data, so values arrive presentation-ready.

## Results

- **About 45 hours saved per run:** work that took most of a week by hand now takes minutes.
- **Over 1,000 hours saved per year:** across a biweekly cadence, that is roughly half of one full-time employee's capacity.
- **Fewer manual errors:** numbers flow straight from the source file instead of being copied by hand 200 times.
- **Strong feedback from both sides:** the analysts who used to build the slides were very positive about the time they got back. Sales leaders were equally positive, since they now received a consistent product on a faster timeline.

## What I Learned

The biggest return came from reuse. I built the slide tool to solve my own reporting problem, and extending it to multiple templates took far less effort than the original build while delivering a larger payoff for a team I hadn't set out to help.

It also showed me how much manual work stays hidden inside teams. Nobody had flagged this process as a problem worth solving until a conversation about a different tool surfaced it. Sharing what you build, even informally, is how those opportunities get found.

## Tech Stack

JavaScript · PowerPoint templates · Excel · Claude

[See the Slide Automation Tool](/slide-automation-tool/) · [← Back to home](/)
