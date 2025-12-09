# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

inventorETB is a Python-based Shopify inventory compiler that processes inventory data exported from Shopify using ShopifyQL queries. It analyzes inventory values, groups products, applies filters based on configurable ranges, and generates detailed reports and filtered CSV outputs.

## Development Commands

### Environment Setup
```bash
# Create virtual environment
python -m venv venv

# Activate virtual environment
source venv/bin/activate   # macOS/Linux
venv/Scripts/activate      # Windows

# Install dependencies
pip install -r requirements.txt
```

### Running the Application
```bash
# Process inventory data
python processQL.py
```

## Architecture

### Core Processing Flow

1. **Data Ingestion**: Reads CSV file exported from Shopify (via ShopifyQL query)
2. **Product Grouping**: Groups duplicate products by title, variant ID, and variant title, aggregating collections into lists
3. **Value Calculation**: Computes per-unit inventory value (retail value / units)
4. **Filtering**: Applies multiple filter types simultaneously:
   - Zero inventory exclusion
   - Min/max value ranges
   - Collection name matching (substring matching, inclusive)
   - Variant ID prefix matching
5. **Output Generation**: Creates filtered CSVs for each range and a summary report

### Configuration System (config.py)

**CSV_FILE_PATH**: Absolute path to Shopify inventory export. Must use raw string (`r"..."`)

**OUTPUT_PATH**: Directory for generated files (defaults to `data/`)

**RANGES**: List of filter configurations, each containing:
- `name`: Descriptive label for the range
- `min`/`max`: Value bounds (optional, omit for unbounded)
- `collection`: List of collection name substrings to match (empty = all collections)
- `variant_ids`: List of specific variant IDs or prefixes to include

### Key Processing Logic

**Product Grouping** (processQL.py:75-101):
- Deduplicates products that appear in multiple collections
- Aggregates collections into a list per unique product variant
- Preserves original inventory values (not summed across duplicates)

**Filter Application** (processQL.py:24-72):
- Filters are applied in sequence: zero inventory → value range → collections → variant IDs
- Collection matching uses substring containment for flexibility
- Variant ID matching uses prefix matching (startswith)

**Output Files**:
- `filtered_{min}_{max}_{collections}_{name}.csv`: One per RANGES entry
- `grouped_inventory.csv`: All products after deduplication
- `inventory_summary.txt`: Statistical summary with counts, values, and averages per range

## ShopifyQL Data Export

The application expects CSV files exported from Shopify using this query:

```sql
FROM inventory
  SHOW ending_inventory_units, ending_inventory_retail_value, product_collection
  GROUP BY product_title, product_variant_id, product_variant_title,
    product_collection WITH TOTALS, GROUP_TOTALS, CURRENCY 'CAD'
  DURING today
  ORDER BY product_variant_id ASC
  LIMIT 1000
```

Expected CSV columns:
- Product title
- Product variant ID
- Product variant title
- Ending inventory units
- Ending inventory retail value
- Product collection

## Common Customizations

**Adding New Product Ranges**: Edit `RANGES` in config.py. For products without standard collections (e.g., shipping, specific SKUs), use `variant_ids` with exact IDs or prefixes.

**Modifying Collection Filters**: Collection matching is substring-based, so "MTG Singles" matches "MTG Singles -- All Products". Use specific substrings to narrow results.

**Changing Output Format**: Modify processQL.py:192-194 for CSV naming or lines 204-207 for summary format.
