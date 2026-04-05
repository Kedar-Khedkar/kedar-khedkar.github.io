---
title: BQ CLI Commands Cheatsheet
description: A quick reference for BQ CLI commands.
author: kedarkhedkar
date: 2026-04-05 10:00:00 +0000
categories: [GCP, BigQuery]    # CATEGORY names should always be lowercase
tags: [Cheatsheet]    # TAG names should always be lowercase
pin: true
image:
    path: /assets/img/posts/bq-cli-cheatsheet.png
    alt: BigQuery CLI Cheatsheet
---

# BigQuery CLI (bq) Cheatsheet

The `bq` command-line tool lets you interact with Google Cloud BigQuery from your terminal. Here are the most common and advanced commands, options, and patterns.

---

## Authentication

```sh
gcloud auth login
gcloud config set project [PROJECT_ID]
```

---

## Table & Dataset Management

**List Datasets**

```sh
bq ls
```

**List Tables in a Dataset**

```sh
bq ls [DATASET]
```

**Show Table Schema and Metadata**

```sh
bq show [DATASET].[TABLE]
bq show --format=prettyjson [DATASET].[TABLE]
```

### Create Dataset

```sh
bq mk [DATASET]
```

**Delete Dataset**

```sh
bq rm -r -d [DATASET]
```

**Create Table**

```sh
bq mk --table [DATASET].[TABLE] [SCHEMA]
# Example schema: name:STRING, age:INTEGER
```

### Delete Table

```sh
bq rm -t [DATASET].[TABLE]
```

**Update Table Schema**

```sh
bq update --table [DATASET].[TABLE] [NEW_SCHEMA]
```

---

## Querying Data

**Run a Query (Standard SQL)**

```sh
bq query --use_legacy_sql=false 'SELECT * FROM [DATASET].[TABLE] LIMIT 10'
```

**Run Query from File**

```sh
bq query --use_legacy_sql=false < query.sql
```

**Save Query Results to Table**

```sh
bq query --destination_table=[DATASET].[TABLE_DEST] --use_legacy_sql=false 'SELECT ...'
```

**Dry Run a Query (for Cost Estimation)**

```sh
bq query --dry_run --use_legacy_sql=false 'SELECT COUNT(*) FROM [DATASET].[TABLE]'
```

**Query with Parameterized Values**

```sh
bq query --parameter='name::STRING:John' --use_legacy_sql=false 'SELECT * FROM [DATASET].[TABLE] WHERE name=@name'
```

---

## Data Import/Export

**Load Data from CSV/JSON/Parquet/Avro**

```sh
bq load --source_format=CSV [DATASET].[TABLE] gs://bucket/file.csv [SCHEMA]
# Schema format: field1:TYPE,field2:TYPE,field3:TYPE
# Example: name:STRING,age:INTEGER,salary:FLOAT,active:BOOLEAN
bq load --source_format=NEWLINE_DELIMITED_JSON [DATASET].[TABLE] gs://bucket/file.json [SCHEMA]
bq load --source_format=PARQUET [DATASET].[TABLE] gs://bucket/file.parquet
bq load --source_format=AVRO [DATASET].[TABLE] gs://bucket/file.avro
```

### Advanced Loading Options

```sh
bq load --replace --skip_leading_rows=1 --field_delimiter="," [DATASET].[TABLE] gs://bucket/file.csv [SCHEMA]
```

**Export Table to GCS**

```sh
bq extract [DATASET].[TABLE] gs://bucket/file.csv
bq extract --destination_format=CSV [DATASET].[TABLE] gs://bucket/file.csv
bq extract --destination_format=PARQUET [DATASET].[TABLE] gs://bucket/file.parquet
bq extract --compression=GZIP [DATASET].[TABLE] gs://bucket/file.csv.gz
```

### Extract Partitioned Table

```sh
bq extract [DATASET].[TABLE$YYYYMMDD] gs://bucket/partitioned.csv
```

---

## Jobs & Monitoring

**Show Job Status**

```sh
bq show -j [JOB_ID]
```

**List Recent Jobs**

```sh
bq ls -j
```

**Cancel a Running Job**

```sh
bq cancel [JOB_ID]
```

---

## Advanced Table Operations

**Copy Table**

```sh
bq cp [DATASET].[TABLE_SRC] [DATASET].[TABLE_DEST]
```

**Table Snapshot**

```sh
bq mk --snapshot [DATASET].[TABLE]@[SNAPSHOT_TIME] [DATASET].[SNAPSHOT_TABLE]
```

**Restore Table from Snapshot**

```sh
bq cp [DATASET].[SNAPSHOT_TABLE] [DATASET].[RESTORED_TABLE]
```

**Partitioned Tables**

```sh
bq mk --table --time_partitioning_type=DAY [DATASET].[TABLE] [SCHEMA]
```

---

## Miscellaneous

**Display Help for Any Command**

```sh
bq help [COMMAND]
```

**Show User Quota and Project Info**

```sh
bq show --format=prettyjson --project_id=[PROJECT_ID]
```

---

## Useful Flags

- `--location=US` : Specify location
- `--max_rows=100` : Limit query results
- `--format=json|csv|pretty` : Output format
- `--replace` : Overwrite destination table/file
- `--compression=GZIP|NONE` : Compression for extract

---

## Example Workflow

```sh
# List all datasets
bq ls

# List tables in dataset
bq ls my_dataset

# Show table schema
bq show my_dataset.my_table

# Query table
bq query --use_legacy_sql=false 'SELECT COUNT(*) FROM my_dataset.my_table'

# Load CSV data
bq load --source_format=CSV my_dataset.my_table gs://my_bucket/my_file.csv name:STRING,age:INTEGER

# Extract table in Parquet format
bq extract --destination_format=PARQUET my_dataset.my_table gs://my_bucket/my_file.parquet
```

---

## References

- [BQ CLI Reference](https://cloud.google.com/bigquery/docs/reference/bq-cli-reference)
- [BigQuery SQL Reference](https://cloud.google.com/bigquery/docs/reference/standard-sql/query-syntax)
