**Yes! That's exactly the flow.** Just one small clarification: Query 2 retrieves Data Properties for all relevant entities discovered by Query 1, not just the Object Properties themselves.

Let's define it clearly.

## **Your two-query architecture**

**Query 1 — Graph discovery**

One SPARQL query

Starting entity: `HQ-PUN-001`

Discover all Object Property relationships up to Hop 3.

Returns:

- Source entity
- Object Property URI
- Target entity URI
- Target entity type
- Hop number

**Query 2 — Data retrieval**

One SPARQL query

Use all discovered entity URIs.

Retrieve:

- All Data Properties
- Property URIs
- Property values
- Datatypes

**Backend — Build dashboard dataset**

Combine relationships and properties into:

- Graph structure (nodes and edges)
- Fact tables
- Statistics
- Widget-ready datasets

**React Dashboard**

KPIs · Charts · Tables · Maps · Filters

## **Example with your Punjab dataset**

**Query 1 returns relationships:**

| Source     | Object Property | Target       | Hop |
| ---------- | --------------- | ------------ | --- |
| HQ-PUN-001 | hasOutlets      | OUT-001      | 1   |
| HQ-PUN-001 | hasOutlets      | OUT-004      | 1   |
| OUT-004    | locatedInCity   | CITY-RWP-001 | 2   |
| OUT-004    | hasManager      | MGR-004      | 2   |

These are illustrative rows from the example dataset.

**Query 2 receives all unique entity URIs:**

```
entity_uris = [    "HQ-PUN-001",    "OUT-001",    "OUT-004",    "CITY-RWP-001",    "MGR-004"]
```

It retrieves all their literal properties, not only selected columns.

For example:

| Entity       | Property          | Value             |
| ------------ | ----------------- | ----------------- |
| OUT-004      | name              | Rawalpindi Saddar |
| OUT-004      | monthlyRevenuePKR | 7600000           |
| OUT-004      | employeeCount     | 27                |
| CITY-RWP-001 | name              | Rawalpindi        |
| MGR-004      | name              | Maryam Iqbal      |
| MGR-004      | performanceRating | 4.3               |

## **Then the backend creates the fact table**

It uses the relationships from Query 1 to join the values from Query 2.

| Outlet            | City       | Manager      | Revenue | Employees |
| ----------------- | ---------- | ------------ | ------- | --------- |
| Rawalpindi Saddar | Rawalpindi | Maryam Iqbal | 7.6M    | 27        |

This is where your graph data becomes useful for analytics.

## **One important technical detail**

Query 2 should use a batched `VALUES` clause containing all discovered entity URIs, rather than making a separate request for every entity.

Also, Query 1 must handle cycles and repeated entities. Deduplicate entity URIs for Query 2, but preserve distinct relationships in the graph.

For large graphs, pagination and limits may be needed. So two queries are the logical design for a dashboard load, not a guarantee that every possible graph can always be fetched in two unbounded requests.

**My recommendation:** Keep these three layers separate:

1. Graph discovery — what is connected?
2. Property retrieval — what information does each entity contain?
3. Data preparation — how do we turn that information into useful analytical datasets?

That separation will make your dashboard much easier to extend when you add new entity types and Object Properties.
