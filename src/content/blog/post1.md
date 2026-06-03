---
title: "🔄 Power BI Incremental Refresh"
description: "Handling Large Datasets Without Breaking Your Gateway: How incremental refresh solves the 'too much data' problem without switching to DirectQuery."
pubDate: "Jun 03 2026"
heroImage: "/post_img.webp"
tags: ["Incremental Refresh", "Power BI", "Large Datasets", "Performance", "Data Analytics", "Data Visualization"]
---

**Introduction**
In our last post, we established that Import Mode is the default powerhouse for Power BI — fast, flexible, and fully featured. But what happens when your data grows beyond what a full refresh can handle? Refreshing 200 million rows of sales history every night is slow, expensive, and fragile. One gateway timeout and your entire dashboard goes stale.

Incremental Refresh is Power BI's answer to this problem. Instead of reloading everything, it only processes the data that has actually changed. In this post, we will break down exactly how it works and how to configure it step by step.

### What Is Incremental Refresh?

Incremental Refresh splits your dataset into two invisible partitions:

- **Historical partition:** A large, frozen chunk of old data that is never touched after the initial load (e.g., everything older than 3 months).
- **Refresh partition:** A small, rolling window of recent data that gets refreshed on every scheduled run (e.g., the last 3 days).

Instead of reloading 200 million rows nightly, Power BI only reloads the 50,000 rows from the last 3 days. The result is dramatically faster refreshes, lower gateway load, and a much more reliable pipeline.

### The Two Magic Parameters

Incremental Refresh is powered by exactly two reserved Power Query parameters that Power BI recognises by name. You must name them precisely:

| Parameter Name | Type | Purpose |
|---|---|---|
| `RangeStart` | Date/Time | The start of the refresh window |
| `RangeEnd` | Date/Time | The end of the refresh window |

Power BI uses these parameters to automatically partition your data. You define them — Power BI manages them at refresh time.

### How to Configure Incremental Refresh: Step by Step

#### Step 1: Create the Parameters in Power Query

1. Open **Power Query Editor** (Home → Transform Data)
2. Go to **Manage Parameters → New Parameter**
3. Create the first parameter:
   - **Name:** `RangeStart`
   - **Type:** `Date/Time`
   - **Current Value:** `1/1/2024 12:00:00 AM`
4. Create the second parameter:
   - **Name:** `RangeEnd`
   - **Type:** `Date/Time`
   - **Current Value:** `6/3/2026 12:00:00 AM`

#### Step 2: Filter Your Date Column Using the Parameters

1. In Power Query, select your main fact table (e.g., `Sales`)
2. Click the dropdown on your date column (e.g., `OrderDate`)
3. Choose **Date/Time Filters → Custom Filter**
4. Set the filter: `OrderDate` is after or equal to `RangeStart` AND before `RangeEnd`
5. Click **OK** and close Power Query

#### Step 3: Define the Incremental Refresh Policy

1. In the **Data** or **Model** view, right-click your fact table
2. Select **Incremental Refresh**
3. Toggle **Incremental Refresh** on
4. Configure the two windows:
   - **Archive data starting:** `3 Years`
   - **Refresh data in the last:** `3 Days`
5. Click **Apply**

#### Step 4: Publish to Power BI Service

Incremental Refresh policies only activate after you publish to the Power BI Service. The partitioning and partition management happen in the cloud, not in Desktop.

### Final Summary: When to Reach for Incremental Refresh

Incremental Refresh sits in the sweet spot between full Import Mode and DirectQuery. Use it when your data is too large for a nightly full refresh but does not require true real-time updates. It gives you the speed of Import Mode with the scalability to handle years of historical data without ever touching a row that has not changed.
