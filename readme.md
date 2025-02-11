# Habitat Classification README

## Overview
This project aims to modify an existing land cover dataset by incorporating habitat interventions, specifically adding patches of scrub and wood pasture within designated polygons. The goal is to scientifically simulate habitat creation while maintaining ecological realism through patch size distribution, density, and spatial patterns.

## Data Structure
### 1. **Land Cover Raster**
- A high-resolution raster dataset (12.5cm pixel resolution) representing existing land cover types.
- Different classes (e.g., Broadleaved Woodland, Scrub, Improved Grassland) are identified by unique numerical codes.

### 2. **Habitat Interventions Shapefile**
- A vector dataset containing polygons representing areas where habitat modifications are planned.
- Each polygon is associated with a habitat type (e.g., "Scrub", "Wood Pasture").

### 3. **JSON Classification File** (`habitat_codes.json`)
- Assigns a habitat **type** and **density level** to each intervention.
- Types: `scrub`, `mixed`, `wood_pasture`, `ignore`.
- Density (scale 1-10) controls patch size, frequency, and clustering.
- Habitats not included in interventions are marked as `ignore` (0).

## Workflow
### 1. **Load Data**
- Read the existing **land cover raster**.
- Load the **habitat intervention polygons**.
- Load **habitat classification JSON** and assign types/densities to polygons.

### 2. **Generate New Features**
- **Scrub Addition:**
  - Patches are created within polygons labeled as `scrub`.
  - Patch size and frequency are controlled by the `density` value.
  - Clustering and irregular edges are introduced for ecological realism.
- **Wood Pasture Addition:**
  - Individual trees and small groves (2–10 pixels) are generated in `wood_pasture` polygons.
  - Density determines tree spacing and group size.

### 3. **Merge & Save Updated Raster**
- Scrub and tree patches are rasterized.
- They are overlaid onto the existing land cover raster without overwriting major features (e.g., rivers, roads).
- The final modified land cover map is saved.

## Adjustments & Customization
- Modify `habitat_codes.json` to change habitat classifications or densities.
- Adjust scrub clustering algorithms to refine patch shapes and spread.
- Use external tools (e.g., FRAGSTATS, GRASS GIS) for landscape analysis.

## Future Improvements
- Introduce **edge constraints** (scrub prefers edges of existing vegetation).
- Incorporate **nearest-neighbor rules** for more realistic spatial distribution.


---



