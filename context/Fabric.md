# Microsoft Fabric Architecture Reference

## Overview

Microsoft Fabric is a unified analytics platform that brings together data engineering, data science, real-time analytics, and business intelligence. It is built on a foundation of Software as a Service (SaaS), providing a single integrated environment.

## Core Components

### Workspaces
- The primary organisational container in Fabric
- Contains all items (lakehouses, notebooks, semantic models, reports)
- Has defined capacity and access controls
- Each workspace maps to a OneLake namespace

### OneLake
- Single, unified data lake for the entire organisation
- Built on Azure Data Lake Storage Gen2 (ADLS Gen2)
- Uses Delta Lake format (Parquet + transaction log) as the default table format
- Supports shortcuts to reference data without copying

### Lakehouses
- Combine the best of data lakes and data warehouses
- Two entry points:
  - **Files**: Unstructured/semi-structured data (CSV, Parquet, JSON, etc.)
  - **Tables**: Structured Delta tables managed by Spark
- Support SQL analytics endpoint for T-SQL querying
- Automatically generate a default semantic model

### PySpark Notebooks
- Apache Spark-based notebooks for data transformation
- Support Python, Scala, SQL, and R
- Can read from and write to lakehouses
- Use `spark.read` and `spark.write` for data operations
- Can be parameterised and scheduled via pipelines

### Pipelines
- Orchestration tool for scheduling and sequencing activities
- Can trigger notebooks, dataflows, and other items
- Support parameters, conditional logic, and error handling

### Semantic Models (Power BI Datasets)
- Define the business logic layer over lakehouse tables
- Include:
  - Tables and columns
  - Relationships (1:1, 1:many, many:many)
  - Measures (DAX expressions)
  - Hierarchies
  - Row-level security (RLS)
- Served via the XMLA endpoint for tooling access

### Power BI Reports
- Visual layer built on semantic models
- Can be authored in Power BI Desktop or Power BI Service
- Support various visualisation types

## Medallion Architecture Pattern

The recommended data architecture pattern in Fabric:

### Bronze Layer (Raw)
- **Purpose**: Land raw data as-is from source systems
- **Lakehouse**: `lh_bronze`
- **Format**: Data stored in original format or minimally transformed
- **Approach**:
  - Ingest CSV/Excel files into the Files section
  - Use notebooks to read files and write as Delta tables
  - Preserve all source columns, add metadata (load_timestamp, source_file)
  - No business logic applied

### Silver Layer (Cleansed/Conformed)
- **Purpose**: Clean, deduplicate, standardise and conform data
- **Lakehouse**: `lh_silver`
- **Approach**:
  - Read from Bronze Delta tables
  - Apply data quality rules (null handling, type casting, validation)
  - Standardise column names and formats
  - Join reference data where needed
  - Apply business rules and calculations from the SOP
  - Write as Delta tables

### Gold Layer (Business-Ready)
- **Purpose**: Business-level aggregations and curated datasets ready for reporting
- **Lakehouse**: `lh_gold`
- **Approach**:
  - Read from Silver Delta tables
  - Apply final business logic, aggregations, and KPI calculations
  - Structure tables for optimal reporting (star/snowflake schema)
  - Create fact and dimension tables
  - This layer feeds the Power BI semantic model

## Fabric REST API

### Key Endpoints
- **Workspaces**: `GET/POST /v1/workspaces`
- **Lakehouses**: `GET/POST /v1/workspaces/{workspaceId}/lakehouses`
- **Notebooks**: `GET/POST /v1/workspaces/{workspaceId}/notebooks`
- **Items**: `GET/POST /v1/workspaces/{workspaceId}/items`

### Item Definition Structure
Fabric items (notebooks, lakehouses) are defined via JSON payloads:

```json
{
  "displayName": "item-name",
  "type": "Notebook",
  "definition": {
    "format": "ipynb",
    "parts": [
      {
        "path": "notebook-content.py",
        "payload": "<base64-encoded-content>",
        "payloadType": "InlineBase64"
      }
    ]
  }
}
```

### Lakehouse Definition
```json
{
  "displayName": "lh_bronze",
  "type": "Lakehouse",
  "description": "Bronze layer lakehouse for raw data ingestion"
}
```

## Shortcuts
- Virtual references to data in other locations
- Types: OneLake shortcuts, ADLS Gen2 shortcuts, S3 shortcuts
- No data duplication — data stays in source
- Useful for referencing source data in Bronze layer

## Best Practices
1. Use Delta Lake format for all managed tables
2. Partition large tables by date or key business columns
3. Use notebooks for complex transformations, Dataflows Gen2 for simple ones
4. Apply medallion architecture for separation of concerns
5. Define semantic models on Gold layer only
6. Use shortcuts to avoid data duplication
7. Parameterise notebooks for reusability
8. Add logging and error handling in notebooks
