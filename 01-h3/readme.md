# Uber Engineering Blog Notes – H3

## Key Takeaways

### Why can't Uber use raw GPS coordinates directly?

Raw GPS coordinates are too granular for large-scale marketplace analysis.

- Every driver and rider has a unique latitude and longitude.
- Comparing millions of exact coordinates is computationally expensive.
- Uber converts nearby coordinates into H3 cells (bucketing) so data can be grouped and analyzed efficiently.

Example:

GPS

↓

H3 Cell A12

↓

Count drivers and riders in A12

Instead of processing every coordinate individually, Uber processes groups of nearby locations.

---

### Why not neighborhoods or postal codes?

Administrative regions are not designed for spatial analysis.

Problems:
- Irregular shapes
- Different sizes
- Different densities
- Inconsistent boundaries between cities and countries

H3 solves this by dividing the world into a uniform hierarchical hexagonal grid.

---

### Why hexagons instead of squares?

Squares have two different neighbor distances.

- Side neighbors are closer.
- Corner neighbors are farther away.

This creates inconsistent neighborhood relationships.

Hexagons:
- Every cell has six neighbors.
- All neighbors are approximately the same distance.
- Better for spatial analysis and nearest-neighbor searches.

---

### What is Resolution?

Resolution controls the size of H3 cells.

Low resolution
- Larger cells
- Less detail
- Better for city or regional analytics

High resolution
- Smaller cells
- More detail
- Better for local analysis (e.g. surge pricing or nearby drivers)

The same GPS coordinate belongs to different H3 cells depending on the chosen resolution.

---

### geoToH3

Converts:

Latitude + Longitude + Resolution

↓

H3 Cell ID

This is the entry point into the H3 system.

---

### kRing (gridDisk)

Returns the center cell and neighboring cells.

Used for:
- Finding nearby drivers
- Expanding the search radius
- Candidate selection before expensive routing calculations

---

### polyfill

Converts a geographic area into H3 cells.

Example:

Kadıköy

↓

[A12, A13, B11, B12...]

Uber can then aggregate trips, drivers, or demand using those cells.

---

## Interview Questions

### Why can't Uber use raw GPS coordinates directly?

Because comparing millions of exact coordinates individually is computationally expensive.
H3 groups nearby locations into cells, enabling efficient aggregation and spatial queries.

---

### Why not use neighborhoods?

Neighborhoods have irregular boundaries and inconsistent sizes.
A uniform grid provides predictable spatial indexing and analysis.

---

### Why are hexagons better than squares?

Squares have two different neighbor distances, while hexagons provide uniform neighbor relationships, making spatial analysis more consistent.

---

### Why doesn't Uber always use the highest H3 resolution?

Higher resolution creates many more cells.

More cells mean:
- More storage
- More computation
- More memory usage

Uber chooses the resolution that provides enough detail for the task.

City-wide analytics → Low resolution

Nearby driver search / Surge pricing → Higher resolution


### Why can't Uber simply use raw GPS coordinates for marketplace analysis?

Uber cannot efficiently analyze millions of raw GPS coordinates because every coordinate is unique. Comparing exact latitude and longitude values for every driver and rider would be computationally expensive. H3 groups nearby locations into hexagonal cells, allowing Uber to aggregate supply and demand and perform spatial queries much more efficiently.

---

### Why are hexagons better than squares?

Hexagons are better for spatial analysis because all neighboring cells are approximately the same distance from the center cell. In a square grid, side neighbors are closer than diagonal neighbors, which creates inconsistencies when searching nearby locations.
Hexagons are better because all neighboring cells are approximately the same distance away. This makes nearby searches and spatial analysis more consistent than square grids.

---

### What is the purpose of H3 resolution?

The purpose of H3 resolution is to control the level of spatial detail. Lower resolutions use larger hexagonal cells, which are suitable for city-level analysis. Higher resolutions use smaller cells, providing more detailed information for street-level analysis, such as finding nearby drivers or applying surge pricing.

---

### If higher resolution gives more detail, why doesn't Uber always use the highest H3 resolution?

Uber doesn't always use the highest resolution because higher resolutions create many more H3 cells. More cells require more memory, storage, and computation. Uber chooses the lowest resolution that provides enough detail for the specific use case.

---

### What does geoToH3() do?
Every user has a GPS location. The geoToH3 function converts the latitude and longitude into an H3 cell ID at a selected resolution.

---
### Imagine there are no drivers in the passenger’s H3 cell. How could Uber use H3 to find nearby drivers?
Uber can use kRing (or gridDisk) to search the neighboring H3 cells around the passenger’s cell. It first checks the immediate neighboring cells, and if no drivers are found, it gradually expands the search radius.

---
### What problem does polyfill() solve?
polyfill() converts a geographic area into a set of H3 cells, allowing Uber to analyze trips, drivers, or demand within that area.

### If you were designing a ride-sharing platform from scratch, would you store GPS coordinates or H3 IDs? Why?
I would store both. GPS coordinates are needed for precise locations and navigation, while H3 IDs are useful for efficient spatial indexing and analysis.

---

### If H3 is so useful, why doesn't Uber completely remove GPS coordinates?
H3 is useful for indexing and analysis, but GPS is still required for precise positioning and routing.


