---
layout: post
title: "Getting Started with Databricks Genie"
subtitle: "How Genie makes natural language data exploration actually work"
date: 2026-09-27
tags: [Databricks, Genie, AI]
---

Databricks Genie is one of those features that sounds like a demo trick until you actually use it on real data. It lets you ask plain English questions against your lakehouse tables and get back SQL, charts, and answers — without writing a single query.

Here's what I've learned using it in practice.

## What Genie actually does

Genie sits on top of your Unity Catalog tables and translates natural language into SQL. Under the hood it uses a large language model that has context about your table schemas, column descriptions, and any instructions you've added to the Genie Space.

The key insight: it's not magic autocomplete. Genie works best when you've done the setup work — clean column names, good table descriptions, and a few example questions baked into the Space configuration.

## Setting up a Genie Space

A **Genie Space** is the container that connects Genie to your data. You configure it in the Databricks UI under AI/BI → Genie.

The three things that matter most:

1. **Tables** — add only the tables relevant to the use case. Fewer tables = better answers.
2. **Instructions** — write plain English rules. For example: *"Revenue is always in USD. Exclude test accounts from all queries."*
3. **Sample questions** — seed the space with 5–10 questions users are likely to ask. This dramatically improves accuracy.

## A real example

I set up a Genie Space for an e-commerce dataset. After adding three tables (orders, customers, products) and a few instructions, I asked:

> *"What were the top 5 products by revenue last quarter?"*

Genie returned the SQL, ran it, and showed a bar chart. No prompt engineering, no schema hunting.

```sql
SELECT
  p.product_name,
  SUM(o.revenue_usd) AS total_revenue
FROM orders o
JOIN products p ON o.product_id = p.product_id
WHERE o.order_date >= DATE_TRUNC('quarter', CURRENT_DATE - INTERVAL 3 MONTHS)
GROUP BY 1
ORDER BY 2 DESC
LIMIT 5
```

## Where it still needs help

Genie struggles with:

- **Multi-hop logic** — questions that require joining 4+ tables
- **Ambiguous business terms** — "active user" means different things to different teams
- **Time zones** — be explicit in your instructions about how timestamps are stored

The fix for all three: more detailed instructions in the Genie Space.

## What's next

In the next post I'll cover how to share a Genie Space with non-technical stakeholders and set up access controls through Unity Catalog.

If you're already using Genie, I'd love to hear what's working — connect with me on [LinkedIn](https://www.linkedin.com/in/ankit-singh-b52035159/).
