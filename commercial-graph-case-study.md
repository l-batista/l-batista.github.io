---
layout: page
title: Commercial Sales Graph Database
subtitle: A Neo4j graph database modeling pharma sales reps, accounts, and referral networks to surface influence patterns invisible to flat CRM data
---

[← Back to home](/)

## The Problem
Pharmaceutical commercial teams track call activity and prescribing behavior in CRM and claims systems, but those systems only show direct cause and effect: a rep called on a doctor, and that doctor prescribed a product. What they miss is peer influence. A physician who has never been called on about a drug can still start prescribing it because a colleague they refer patients to already does. That pattern is a multi-hop relationship, not a row in a table, and it is effectively invisible to standard SQL-based CRM reporting.

## The Solution
I built a Neo4j graph database modeling the full structure of a commercial sales organization: reps, territories, accounts, products, call activity, prescribing events, and a physician referral network. On top of that, I wrote Cypher queries and ran graph algorithms to surface network-driven prescribing patterns and identify which accounts function as influence hubs within their referral network.

## How It Works

**1. Schema design**
- `Rep` –`COVERS`→ `Territory`
- `Account` –`LOCATED_IN`→ `Territory`
- `Rep` –`CALLED_ON`→ `Account` (carries `product_id`, `date`, `notes`)
- `Account` –`PRESCRIBED`→ `Product` (carries `date`, `volume`)
- `Account` –`REFERS_TO`→ `Account` (the physician referral network)

**2. Synthetic data generation (Python)**
- Generated a seeded, reproducible dataset: 15 reps, 10 territories, 150 accounts, 8 products across 4 therapeutic areas, ~2,500 calls, and ~1,166 prescribing events
- Built in two intentional signals so the graph would have real patterns to find: call activity correlating with prescribing, and a referral-neighbor effect where an account's prescribing habits influence accounts that refer into it, independent of direct rep contact

**3. Database build (Neo4j AuraDB)**
- Loaded the CSVs through Aura's Data Importer, mapping each file to node labels or relationship types
- Added `NODE_KEY` constraints on every entity ID to enforce uniqueness and catch import errors early

**4. Analysis (Cypher + Graph Data Science library)**
- Wrote traversal queries to detect indirect, network-driven prescribing
- Ran betweenness centrality (via Neo4j's Graph Data Science library) over the referral network to rank accounts by how central they are as connectors between otherwise separate parts of the network

## Example Queries and Outputs

**Query 1: Find accounts prescribing a product with no direct call history for it, driven by a referral neighbor**
```cypher
MATCH (a:Account)-[:REFERS_TO]-(neighbor:Account)-[:PRESCRIBED]->(p:Product)
WHERE EXISTS { MATCH (a)-[:PRESCRIBED]->(p) }
  AND NOT EXISTS {
    MATCH (a)<-[c:CALLED_ON]-(:Rep) WHERE c.product_id = p.product_id
  }
RETURN DISTINCT a.name AS account_without_direct_calls, p.name AS product, neighbor.name AS referral_neighbor
LIMIT 25;
```
**Output:**

| account_without_direct_calls | product | referral_neighbor |
|---|---|---|
| Jeffrey Meyer | Guyex | Amy Silva |
| Mary Nguyen | Surfaceor | Jeffrey Johnson |
| Michael Elliott | Pmex | Matthew Lucas |

Each of these accounts prescribed the listed product despite no rep ever calling on them about it. In every case, a referral neighbor with 3 or more prescriptions of that same product exists, consistent with the account picking up the prescribing behavior through peer influence rather than direct sales contact.

**Query 2: Rank accounts by betweenness centrality within the referral network, to find structural bridges**
```cypher
CALL gds.betweenness.stream('referral-network')
YIELD nodeId, score
RETURN gds.util.asNode(nodeId).name AS account, score
ORDER BY score DESC
LIMIT 10;
```
**Output:**

| account | score |
|---|---|
| Clifford Ford | 14.0 |
| Christopher Bass | 11.0 |
| Kendra Maddox DVM | 10.0 |
| Joyce Solis | 10.0 |
| Lauren Daniels | 8.5 |
| Keith Sullivan | 8.33 |
| Sean Rasmussen | 8.33 |

Clifford Ford's score of 14.0 marks him as the single most structurally central account in his referral cluster: six other accounts connect through him directly or via one additional hop, making him the highest-leverage target for driving prescribing activity outward through peer influence.

## Results
- **Surfaced a pattern flat CRM reporting cannot see:** identified specific accounts whose prescribing is explained by peer referral influence rather than direct rep activity, information with no equivalent field in a standard CRM record.
- **Replaced a manual heuristic with a proper algorithm:** an early hand-written Cypher query for finding "bridge" accounts returned zero results; switching to a betweenness centrality algorithm from Neo4j's Graph Data Science library correctly identified them.
- **Established a reusable graph foundation:** the schema and constraints are structured to extend cleanly, additional relationship types (such as a document knowledge layer) can attach to the existing `Account` and `Product` nodes without remodeling the graph.

## What I Learned
This was a hands-on introduction to graph databases, and it sharpened exactly where a graph earns its value over a relational database. Basic aggregation, such as counting calls per account, is no easier in Cypher than in SQL, and claiming otherwise would be overselling the technology. The real advantage shows up in variable-depth traversal and network-shape questions: tracing indirect influence through a referral chain, or ranking nodes by structural centrality, are the kinds of queries that get complex and slow in SQL but stay natural in Cypher. I also learned to trust purpose-built graph algorithms over hand-rolled logic for anything involving network structure, since my manual attempt at finding influential accounts missed patterns that a real centrality algorithm caught immediately.

## Tech Stack
Python (Faker) · Neo4j AuraDB · Cypher · Neo4j Graph Data Science Library

## What's Next
The next phase of this project connects a document knowledge layer, product one-pagers, competitive battlecards, and payer access notes, into the same graph, with the goal of powering a GraphRAG-based sales agent that can answer field rep questions using both structured account data and unstructured reference material in a single query. That phase has not been built yet.
