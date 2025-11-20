# NEURALTWIN 3D Product Placement Metadata Guide

> Version: v3  
> Status: Draft (internal spec)

---

## Table of Contents

- [0 Document Overview](#0-document-overview)
- [1 Concept and Scope](#1-concept-and-scope)
- [2 Files](#2-files)
- [3 IDs and Naming](#3-ids-and-naming)
  - [3.1 Furniture Categories and IDs](#31-furniture-categories-and-ids)
  - [3.2 Product Categories and IDs](#32-product-categories-and-ids)
  - [3.3 GLB File Naming](#33-glb-file-naming)
- [4 CSV Specifications](#4-csv-specifications)
  - [4.1 store_master.csv](#41-store_mastercsv)
  - [4.2 furniture_layout.csv](#42-furniture_layoutcsv)
  - [4.3 product_placement.csv](#43-product_placementcsv)
- [5 dp_allowed_categories Conventions](#5-dp_allowed_categories-conventions)
- [6 Authoring Checklist](#6-authoring-checklist)

---

## 0 Document Overview

This document defines the **CSV-based metadata format** used by NEURALTWIN to describe:

- Offline store 3D layouts (Furniture placement)
- Product placement on fixtures
- Basic store metadata

This spec is designed for:

- 3D rendering (three.js + glb assets)
- Analysis / Simulation (AI engine input)
- Consistent authoring by non-engineers (Excel, Google Sheets, etc.)

Current scope:

- **In scope**
  - Store list
  - Furniture instances (3D fixtures, decor, etc.)
  - Product placement on furniture
  - Basic ID and naming conventions
- **Out of scope (for now)**
  - Ontology graph tables
  - Zone definitions
  - Time-series sales/traffic data
  - Scene-level AI analysis

---

## 1 Concept and Scope

3D assets are grouped into three main types:

1. **Store**  
   - 3D model of the store shell (walls, floor, ceiling, etc.)  
   - Typically one per store.
2. **Furniture**  
   - All fixtures and objects that build the store environment:
     - Shelves, racks, hangers, tables
     - Mannequins, display platforms, display stands
     - Cashier counters, fitting rooms
     - Mirrors, benches, sofas, logo objects, decor
3. **Product**  
   - Items the brand sells (apparel, footwear, bags, accessories, etc.)

This spec focuses on **metadata for Product Placement**:

- Where fixtures (Furniture) are placed (position, rotation)
- Which fixtures are display-capable and for which product categories
- Which products are displayed on which fixtures

---

## 2 Files

We use three CSV files:

1. `store_master.csv`  
   Store-level master data.

2. `furniture_layout.csv`  
   All furniture instances in each store, with:
   - Model IDs
   - glb filenames
   - Position / rotation
   - Mobility
   - Display capability

3. `product_placement.csv`  
   Product–furniture relationships:
   - Which product is displayed on which furniture instance
   - Approximate displayed quantity

All files:

- Format: CSV
- Encoding: UTF-8
- First row: header (column names)
- Separator: `,`
- Decimal separator: `.` (e.g. `2.5`)

---

## 3 IDs and Naming

### 3.1 Furniture Categories and IDs

#### 3.1.1 Furniture Categories (logical)

NEURALTWIN uses the following **furniture categories**:

- Shelf
- Rack
- Hanger
- Table
- Mannequin
- Display Platform
- Display Stand
- Cashier
- Fitting Room
- Decor
- Other

#### 3.1.2 `furniture_category` codes (CSV values)

In `furniture_layout.csv`, the `furniture_category` column uses these **uppercase codes**:

| Category Name     | `furniture_category` value |
| ----------------- | -------------------------- |
| Shelf             | `SHELF`                    |
| Rack              | `RACK`                     |
| Hanger            | `HANGER`                  |
| Table             | `TABLE`                    |
| Mannequin         | `MANNEQUIN`               |
| Display Platform  | `DISPLAY_PLATFORM`        |
| Display Stand     | `DISPLAY_STAND`           |
| Cashier           | `CASHIER`                  |
| Fitting Room      | `FITTING_ROOM`            |
| Decor             | `DECOR`                    |
| Other             | `OTHER`                    |

> Examples:
> - Mirror → `DECOR`
> - Bench / sofa → `DECOR`
> - Brand logo object → `DECOR`
> - Pure display shelf → `SHELF`
> - Hanging fixture → `HANGER`
> - Mixed rack structure → `RACK`

#### 3.1.3 Furniture model IDs (`furniture_model_id`)

`furniture_model_id` is the **type/entity ID** for each furniture model.

- Format: `<2-letter prefix><3-digit number>`

Prefixes per furniture category:

| `furniture_category` | Prefix | Example `furniture_model_id` |
| -------------------- | ------ | ---------------------------- |
| `SHELF`              | `SF`   | `SF001`                      |
| `RACK`               | `RK`   | `RK001`                      |
| `HANGER`             | `HG`   | `HG001`                      |
| `TABLE`              | `TB`   | `TB001`                      |
| `MANNEQUIN`          | `MN`   | `MN001`                      |
| `DISPLAY_PLATFORM`   | `PF`   | `PF001`                      |
| `DISPLAY_STAND`      | `DS`   | `DS001`                      |
| `CASHIER`            | `CS`   | `CS001`                      |
| `FITTING_ROOM`       | `FR`   | `FR001`                      |
| `DECOR`              | `DC`   | `DC001`                      |
| `OTHER`              | `OT`   | `OT001`                      |

Examples:

- Round display table → `TABLE` → `TB001`
- Double-rail hanger → `HANGER` → `HG001`
- Back wall shelf type 1 → `SHELF` → `SF001`
- Entrance plinth → `DISPLAY_PLATFORM` → `PF001`
- Mirror near fitting room → `DECOR` → `DC001`

#### 3.1.4 Furniture instance IDs (`furniture_instance_id`)

`furniture_instance_id` identifies each **physical instance** of furniture in a store.

- Format: `<furniture_model_id>_<2-digit instance number>`
  - Examples:
    - `TB001_01`, `TB001_02`
    - `HG001_01`
    - `DC001_01`

Semantics:

- `TB001` = one table model
- `TB001_01`, `TB001_02` = two actual tables in the store, using the same model.

---

### 3.2 Product Categories and IDs

#### 3.2.1 Product categories (`product_category`)

We use the following product category codes:

- `TOPS`
- `BOTTOMS`
- `OUTER`
- `DRESS`
- `SHOES`
- `BAGS`
- `ACCESSORY`
- `HAT`
- `SCARF`
- `GLOVES`

These are used in:

- `product_placement.csv` → `product_category`
- `furniture_layout.csv` → `dp_allowed_categories` (allowed categories on fixtures)

#### 3.2.2 Product IDs (`product_id`)

`product_id` is an **internal product model ID**, not necessarily the full retail SKU.

- Format: `<2-letter prefix><3-digit number>`

Suggested prefixes:

| `product_category` | Prefix | Example `product_id` |
| ------------------ | ------ | -------------------- |
| `TOPS`             | `TP`   | `TP001`              |
| `BOTTOMS`          | `BT`   | `BT001`              |
| `OUTER`            | `OT`   | `OT001`              |
| `DRESS`            | `DR`   | `DR001`              |
| `SHOES`            | `SH`   | `SH001`              |
| `BAGS`             | `BG`   | `BG001`              |
| `ACCESSORY`        | `AC`   | `AC001`              |
| `HAT`              | `HT`   | `HT001`              |
| `SCARF`            | `SC`   | `SC001`              |
| `GLOVES`           | `GV`   | `GV001`              |

Examples:

- White sneakers → `SH001`
- Black tote bag → `BG001`
- Striped T-shirt → `TP001`
- Trench coat → `OT001`

> If needed later, a separate `brand_sku` column can be added for real-world SKUs.

---

### 3.3 GLB File Naming

We explicitly store the glb filenames in the CSV.

#### 3.3.1 Furniture glb files (`furniture_glb_file`)

- Column: `furniture_glb_file` in `furniture_layout.csv`
- Suggested format:  
  `<furniture_model_id>_<human-readable-name>.glb`

Examples:

- `TB001_Round_display_table.glb`
- `HG001_Double_rail_hanger.glb`
- `DC001_Mirror.glb`

Runtime loading example:

```ts
const FURNITURE_BASE_URL = "https://storage/nt/furniture/";

function getFurnitureGlbUrl(filename: string) {
  return `${FURNITURE_BASE_URL}${filename}`;
}
