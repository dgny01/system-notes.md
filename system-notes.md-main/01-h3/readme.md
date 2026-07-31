# H3 - Uber's Hexagonal Hierarchical Spatial Index

## What problem does it solve?

Uber receives millions of GPS events every day.

Working directly with latitude and longitude does not scale because every coordinate is unique.

H3 converts GPS coordinates into hierarchical hexagonal cells so that spatial data can be grouped, queried and analyzed efficiently.

---

## Why not use latitude/longitude?

Latitude and longitude are continuous values.

They are difficult to aggregate.

Finding nearby drivers would require calculating distances against millions of locations.

---

## Why hexagons?

Hexagons have six neighbors at equal distance.

This makes neighborhood queries, smoothing and spatial analysis more consistent than square grids.

---

## How Uber uses H3

Passenger Location

↓

geoToH3()

↓

H3 Cell ID

↓

Dispatch

↓

Ride Matching

↓

Pricing

↓

Analytics

↓

Machine Learning

---

## Key Engineering Idea

Instead of searching every driver in a city,

Uber first searches nearby H3 cells using kRing.

Only drivers inside those cells become candidates.

The expensive distance calculation is performed only on a small subset.

This significantly improves scalability.

---

## Production Notes

• Resolution is a trade-off between precision and search cost.

• Pentagon cells are unavoidable because of spherical geometry.

• Compact representation reduces storage.

• H3 IDs are 64-bit integers, making them efficient keys in distributed systems.

---

## Interview Takeaways

Why hexagons instead of squares?

How does H3 reduce Ride Matching complexity?

What is kRing?

Why does H3 scale better than global search?

---

## New Things I Learned

- GPS coordinates are not suitable for aggregation.

- H3 transforms spatial problems into integer lookups.

- Ride Matching becomes a local search problem instead of a global search problem.