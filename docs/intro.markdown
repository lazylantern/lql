---
layout: page
title: "Showcasing LQL, an internal lightweight SQL layer for behavioral analysis"
permalink: intro
---

Here at Lantern, we spend a lot of time digging into product data. Our data science team spends hours crunching the raw data delivered by Segment to extract new relevant insights for our customers.

Despite following a well documented [schema](https://segment.com/docs/connections/storage/warehouses/schema/), this raw data is not always convenient to work with using SQL queries, especially when it comes to behavioral analysis. Account level analysis (for B2B) or session based analysis require either some preliminary transformations or epic SQL skills. As we approach these datasets with an R&D angle, precomputed transformations show their [limits](https://benn.substack.com/p/metrics-layer).

In order to double down on SQL's universal reach, we came up with an internal SQL frontend that expands regular SQL. We dubbed it LQL and it provides helper functions dedicated to behavioral analysis.

## A snippet is worth a thousand words

Let's start with a funnel query for a B2B product, with steps in a given order, and some added constraints and filters:

> Find all organizations who performed all steps of the funnel (signup, add_credit_card, signed_form) with less than 5 minutes between every step without ever reaching out to support
>

The plain raw SQL query for this analysis is a [100+ lines long nightmare](https://gist.github.com/GuillaumeLachaud/1142e13e014e01b12a5de3f30107c63e)! It's very tedious, with error-prone repetitions. Also, who volunteers to maintain it?

Here is the same query in LQL:

```sql
SELECT
  org_id,
  org_name
FROM
  organization
WHERE
  did(org_id, "sign_up")
  AND then_did(org_id, "add_credit_card", "<=5m")
  AND then_did(org_id, "signed_form", "<=5m")
  AND never_did(org_id, "chat_started")
```

Much shorter and easier to maintain.

That's just one example of what we can achieve with LQL. We have many more operators and functions in stores to bring painless behavioral analysis in existing SQL workflows.

As an added bonus, our customer success team recently started to plug Metabase to our SQL proxy to directly plot average session length and their forecasted evolution for our customers. We even use these kind of high level queries in reverse ETL workflows!

We're thinking about releasing and/or open-sourcing this publicly for anyone to use. As you can imagine, there's some cleanup and documentation work that the codebase must undergo before this could happen. Would it make sense to release such a tool? Would you have any use for it?

Please share your honest feedback and join our [github project](https://github.com/lazylantern/lql) to get involved, it will be highly appreciated!

You can also subscribe below to receive further updates about the project!

Thanks!

{% include subscribe-form.html %}
