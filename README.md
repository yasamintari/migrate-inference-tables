# Migrate Inference Tables

Migrate inference tables from the legacy **v1 AI Gateway** to the new **Unity AI Gateway** in Databricks.

## Overview

This notebook automates the migration of inference tables (request/response logs from model serving endpoints) from the v1 AI Gateway architecture to Unity AI Gateway. This migration is necessary when upgrading your model serving infrastructure to take advantage of Unity Catalog's governance, lineage tracking, and centralized access control.

## What Gets Migrated

- **Request logs**: Input payloads sent to model endpoints
- **Response logs**: Model predictions and outputs
- **Metadata**: Timestamps, endpoint names, request IDs, and other tracking information

## Prerequisites

- Databricks workspace with Unity Catalog enabled
- Access to both the source v1 AI Gateway inference tables and the target Unity Catalog
- Appropriate permissions:
  - `SELECT` on source inference tables
  - `CREATE TABLE` on target catalog/schema
  - `WRITE` on target location

## Usage

### 1. Configure Parameters

Update the following parameters in the notebook:

| Parameter | Description |
|-----------|-------------|
| `source_catalog` | Source catalog containing v1 inference tables |
| `source_schema` | Source schema name |
| `target_catalog` | Destination Unity Catalog |
| `target_schema` | Destination schema |
| `endpoint_names` | List of endpoints to migrate (or `*` for all) |

### 2. Run the Migration

1. Attach the notebook to a cluster with Unity Catalog access
2. Run all cells sequentially
3. Monitor the migration progress in the output

### 3. Validate Results

After migration completes:
- Verify row counts match between source and target
- Spot-check sample records for data integrity
- Update any downstream dashboards or queries to point to the new tables

## Architecture

```
v1 AI Gateway                    Unity AI Gateway
┌──────────────────┐            ┌──────────────────┐
│  Inference       │            │  Unity Catalog   │
│  Tables (HMS)    │  ────────> │  Inference       │
│                  │   migrate  │  Tables          │
└──────────────────┘            └──────────────────┘
```

## Notes

- The migration is idempotent - running it multiple times will not create duplicates
- Large tables are processed in batches to avoid memory issues
- Schema evolution is handled automatically

## Troubleshooting

| Issue | Solution |
|-------|----------|
| Permission denied | Ensure you have `SELECT` on source and `CREATE TABLE` on target |
| Table not found | Verify the endpoint name and that inference logging was enabled |
| Schema mismatch | Check if source table has custom columns not in the target schema |

## Resources

- [Unity AI Gateway Documentation](https://docs.databricks.com/en/machine-learning/model-serving/inference-tables.html)
- [Migrating to Unity Catalog](https://docs.databricks.com/en/data-governance/unity-catalog/index.html)
