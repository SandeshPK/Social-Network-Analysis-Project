# Social Network Analysis of Amazon Product Reviews (Musical Instruments)

## Overview
This project models Amazon customer reviews as networks and uses community detection to see how reviewer and product groupings changed over time. Reviews become graphs (reviewer-reviewer and product-product), and the resulting communities from an early period (2004-2012) are compared against a later one (2013-2016).

## Dataset
- **Source:** `reviews_Musical_Instruments_5.json.gz`, the 5-core Musical Instruments subset of the Amazon Reviews dataset, loaded from Google Drive.
- **Size:** 10,261 reviews.
- **Fields used:** `reviewerID`, `asin` (product ID), `overall` (star rating, 1-5), `reviewTime` (parsed for the review year).
- **Time split:**
  - Group 1: 2004-2012, 3,527 reviews
  - Group 2: 2013-2016, 6,734 reviews

## Problem Statement
A star rating on its own doesn't say much about how reviewers relate to each other. This project asks whether reviewers and products form distinct communities based on shared ratings, shared products, and shared popularity, and whether those communities changed shape as the platform grew. That matters because a shift toward larger, denser communities, especially negative ones, can point to things a simple average rating would miss.

## Technology & Algorithms
- **Environment:** Python, Google Colab
- **Libraries:** `pandas` for data handling, `networkx` for building and drawing graphs, `matplotlib` for plotting, `numpy`, and `python-louvain` (`community_louvain`) for community detection

**Graphs built for each time period:**

| Graph | Nodes | Edge rule |
|---|---|---|
| `graph_pos` | Reviewer-Reviewer | Both reviewers rated the same product 4 or 5 stars |
| `graph_neg` | Reviewer-Reviewer | Either reviewer gave a rating of 1, 2, or 3 |
| `graph_product` | Reviewer-Reviewer | Both reviewers reviewed the same product, any rating |
| `graph_product_pop` | Product-Product | Both products were reviewed by the same reviewer |

Community detection used the Louvain method (`community_louvain.best_partition`), run on all 8 graphs (4 network types across 2 time periods). Communities were drawn with a spring layout and colored by group, and average community size was calculated for each graph.

## Results
Average community size, 2004-2012 vs. 2013-2016:

| Network Type | 2004-2012 | 2013-2016 | Growth |
|---|---|---|---|
| Positive-review community (`graph_pos`) | 101 | 161 | +59% |
| Product-review community (`graph_product`) | 74 | 156 | +111% |
| Product-popularity community (`graph_product_pop`) | 74 | 121 | +64% |
| Negative-review community (`graph_neg`) | 7 | 220 | +3043% |

## Business Insight & Conclusion
All four community types got bigger in the later period, which lines up with review volume roughly doubling. But the growth wasn't even. Positive-review and product-based communities grew by 60-110%, which is roughly what you'd expect from a bigger, more active reviewer base. The negative-review community is the outlier: it went from an average size of 7 to 220.

That's a big enough jump to be worth a closer look. Unhappy reviewers went from scattered, mostly disconnected individuals to large, tightly linked groups clustered around the same products. It could mean real quality problems concentrated in a handful of products, it could reflect more coordinated or copied negative reviews, or it could just be that negative reviews reinforce each other more once there's enough volume on the platform. Either way, tracking negative-community size alongside the usual average-rating metrics looks like a reasonable early warning signal, and the products anchoring these large negative clusters are a good place to start digging.
