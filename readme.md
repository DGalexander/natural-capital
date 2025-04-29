# Habitat Classification README

# **Workflow Guide: MCDA for Tree and Scrub Placement Using Natural Capital Layers**

## **📌 Overview**
This workflow guides the integration of ecological data, ecosystem service layers (InVEST models), and Multi-Criteria Decision Analysis (MCDA) to prioritize tree and scrub placement within a landscape.

**Goals:**
- Use real ecological data (`percent_sc`, `percent_wo`) instead of estimates.
- Integrate **Carbon, Water Yield, and Sediment Retention** to optimize tree and scrub placement.
- Ensure raster-based compatibility while considering overlapping polygons.
- Produce multiple scenarios (e.g., **low, medium, high**) to showcase different levels of intervention.

---

## **📂 1. Input Data**
### **🔷 1.1 Utopia Habitat Shapefile**
📍 **File:** `../data/boundaries/MHC_Utopia_Stage_2/MHC_Utopia_Stage_2.shp`  
📌 **Columns Used:**
| Column | Description |
|--------|-------------|
| `percent_sc` | Percentage of polygon allocated to scrub |
| `percent_wo` | Percentage of polygon allocated to woodland |

✅ **Notes:**
- Only ~116 polygons have values; the rest are 0.
- These percentages **directly control** how much scrub/trees we add.

---

### **🔷 1.2 Natural Capital (NC) Raster Layers**
These layers provide ecosystem service values for decision-making.

#### **🌳 1.2.1 Carbon Stock (SOC)**
📍 **File:** `data/carbon/morr_C_tha_hires.tif`  
📌 **Interpretation:** 
- Higher values = **more carbon stored in soil**.
- **Guidance:**  
  - Plant **trees in low-carbon** areas to increase carbon sequestration.
  - Avoid tree planting in high-carbon soils (**penalty applied**).

#### **💧 1.2.2 Seasonal Water Yield (SWY)**
📍 **File:** `data/swy/morridge/hires_lcm/QF_morr_hr.tif`  
📌 **Interpretation:** 
- High values = **higher surface runoff** (increased flood risk).
- **Guidance:**  
  - Prioritize **scrub** in high runoff areas for water retention.

#### **🌱 1.2.3 Sediment Retention (Erosion Control)**
📍 **Key Files:**
- **Positive Contributions (Higher = More Retention = More Protection)**
  - ✅ `tha_avoided_erosion_hr125sq.tif` → **Soil erosion avoided** (tons/ha/year)
  - ✅ `tha_avoided_export_hr125sq.tif` → **Sediment export avoided** (tons/ha/year)

- **Negative Contributions (Higher = More Erosion = More Risk)**
  - ❌ `tha_sed_export_hr125sq.tif` → **Sediment leaving catchment** (tons/ha/year)
  - ❌ `tha_stream_hr125sq.tif` → **Sediment reaching streams** (tons/ha/year)
  - ❌ `tha_usle_hr125sq.tif` → **Universal Soil Loss (USLE) model erosion** (tons/ha/year)

📌 **Interpretation:**
- High **erosion risk** → **more trees & scrub needed**.
- We create a **combined Sediment Retention Index (SRI)**:
  
  \[
  \text{SRI} = (\text{avoided erosion} + \text{avoided export}) - (\text{sediment export} + \text{sediment in streams} + \text{USLE erosion})
  \]
  
- **Guidance:**
  - **Higher SRI** → Trees & Scrub are helpful (**positive weight**).
  - **Lower SRI** → High erosion risk (**stronger weight for trees/scrub**).

---

## **🔍 2. Data Preprocessing**
### **📌 2.1 Normalize All NC Layers (0 to 1)**
For each raster:
\[
X'_{\text{normalized}} = \frac{X_{\text{raster}} - X_{\text{min}}}{X_{\text{max}} - X_{\text{min}}}
\]
- This ensures all values are **comparable**.
- Each **polygon gets a zonal stat (mean) per layer**.

### **📌 2.2 Compute Multi-Criteria Decision Index (MCDA)**
Each polygon gets an **MCDA Score** based on NC layers.

\[
MCDA = w_1 \cdot C' + w_2 \cdot SWY' + w_3 \cdot SRI'
\]
where:
- **\( C' \) (Carbon Score)** → Penalize trees in high-carbon soils.
- **\( SWY' \) (Water Yield Score)** → Favor scrub in high runoff areas.
- **\( SRI' \) (Sediment Retention Score)** → Favor trees/scrub in erosion-prone areas.
- **\( w_i \) = Weights** (Tunable: e.g., 0.3 Carbon, 0.4 SWY, 0.3 SRI).

---

## **🔲 3. Spatial Implementation**
### **📌 3.1 Handling Overlapping Polygons**
Since polygons **overlap raster tiles**, we use:
1. **Zonal Stats per Polygon** (mean per raster layer).
2. **Rasterize Percent Scrub/Woodland** per 1km tile.
3. **Combine Percent Data with NC Suitability Map**.

**Should we use a VRT file?**
- A **VRT (Virtual Raster)** allows treating all tiles as a single raster.
- This is useful when processing **large-scale data**, but **since we process per polygon, we can avoid it**.

### **📌 3.2 Raster Output**
For each scenario:
1. Generate a **scrub raster** (based on `percent_sc` + MCDA weight).
2. Generate a **tree raster** (based on `percent_wo` + MCDA weight).
3. Merge into **final land cover raster**.

---

## **🎯 4. Final Scenarios**
We run **3 scenarios** adjusting **percent_sc** and **percent_wo**:
| Scenario | Scrub Increase | Tree Increase |
|----------|---------------|--------------|
| **Low** | Baseline values | Baseline values |
| **Medium** | +25% | +25% |
| **High** | +50% | +50% |

✅ **Final Outputs:**
- **Raster Maps (Scrub & Trees)**
- **Shapefiles for Land Cover Changes**
- **Comparison Metrics** (New pixels added)

---

## **📌 Next Steps: Implement the Code**
Now that the workflow is structured, would you like **step-by-step coding** for:
1. **NC Layer Normalization**
2. **Zonal Statistics per Polygon**
3. **MCDA Score Calculation**
4. **Final Raster Updates**
5. **Shapefile Generation**





