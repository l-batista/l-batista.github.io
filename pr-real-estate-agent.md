---
layout: page
title: PR Real Estate Intelligence Agent
subtitle: An AI agent combining SQL queries and RAG retrieval to surface property investment opportunities
---

# Puerto Rico Real Estate Intelligence Agent
*An AI agent that combines SQL queries and RAG retrieval to surface property investment opportunities*

[← Back to home](/)

## The Problem
Puerto Rico's largest classifieds site lists thousands of properties, but finding
investment opportunities (short-term rental potential, beach access, amenities)
means manually scrolling listings across 86 municipalities using a handful of rigid
filters. There is no way to ask questions like "Which listings under $300K near the
beach have STR potential?"

## The Solution
I built an end-to-end system: a Python pipeline that periodically collects and
enriches listings into a database, and a Langflow AI agent that answers
plain-English questions using that data plus domain knowledge.

## How It Works

**1. Data pipeline (Python)**
- Scrapes listings municipality by municipality across all 86 locations
- Three auto-detecting run modes (initial, weekly, daily), so no manual setup per run
- Deduplicates on each listing's source ID and handles pagination and Spanish-language
  character encoding

**2. Enrichment**
- Adds GPS coordinates and agent details
- Flags investment signals via keywords: beach access, STR potential, pool

**3. Storage (SQLite)**
- 8,000+ active listings, refreshed on each pipeline run
- Database triggers automatically record price history, making price drops trackable

**4. AI agent (Langflow)**
The agent chooses between two tools depending on the question:
- `run_sql_query`: queries the SQLite database for listings and numbers
- `search_documents`: RAG retrieval over a FAISS vector store holding three reference
  documents (real estate domain knowledge, STR market data, municipality mappings)

The RAG layer gives the agent local context, such as how municipalities map to
regions and what drives short-term rental demand, so its SQL queries and answers
are more precise.

## Example
**Prompt:** "What are all apartment types that have a beach view? Summarize results by municipality and include average prices. 
Exclude any apartments that do not have a room count."\
**Answer:** "56 apartments across 28 municipalities"
<video controls width="100%">
  <source src="/PRagentdemo.mp4" type="video/mp4">
</video>

## Results
- **Turned an unusable search into a useful tool:** the original site's limited,
  fixed filters made targeted searching impractical.
- **Hours to under a minute:** a search that could take over an hour manually now
  takes less than a minute.
- **More flexible, precise searching:** instead of preset filters, the agent can
  combine multiple search criteria to return listings that precisely match what
  you're looking for.

## What I Learned
This was a learning project, and building a working prototype taught me how agent
architecture fits together in practice. I saw how effective it is to give an agent
direct access to query a database, and how RAG improves results, especially when
combined with the other tools the agent can use.

## Tech Stack
Python · SQLite · FAISS · RAG · Langflow · LLM agents
