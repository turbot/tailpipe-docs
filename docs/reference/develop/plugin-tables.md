---
title: Plugin Tables
---

# Plugin Table Steps

## Step 1: Table Schema

The initial step to building a table is to define the schema, this should be as close in content as possible to the data available from the log entry the table is designed to represent.

These row structs should live in the `tables/<table_name>` directory of the plugin, and consist of the fields to match the log entry as close as possible, the fields must have `json` tags to define the name of the column which should be **snake_case** and optional nullability of the column.

Additionally, the row struct should also embed the `schema.CommonFields` struct from the SDK, this is to ensure every table will have the common fields.

You can also override the column descriptions for this schema by providing a function with the signature `GetColumnDescriptions() map[string]string`.

> NOTE: Not all data types are supported.

## Step 2: Data Source(s)

Next we need to ask ourselves how we obtain our log entries, which sources do we want to support for our log entries.

Some sources may be already provided in the plugin or SDK such as `AwsS3BucketSource` which can be used to obtain files from S3.

However, may need to implement a custom source for example if logs are only available from an API.

In this event we should create a custom source in our `sources/<source_name` directory, which should embed `row_source.RowSourceImpl` and implement the following functions:
- `Identifier()` returning the name of the source.
- `Collect(ctx context.Context)` responsible for obtaining the log entries.
- _Optionally_ `Init(ctx context.Context, params *row_source.RowSourceParams, opts ...row_source.RowSourceOption)` if any custom start up logic is required (setting of collection state func, etc.)

A source **should** implement collection state.

### Source Config

If configuration is required, define a schema struct also in the `sources/<source_name>` package named `{source_name}_config.go` and implement the following functions:
- `Identifier()` returning the name of the source.
- `Validate()` a validation function for the provided config.

### Collection State

Collection state is required for resumption of log collection to ensure that the same logs are not collected multiple times (with some instances offering benefit of allowing performance benefit by adjusting the starting offset).

The `SDK` provides a few varying types of collection state, however if none of these apply to the source, you can create a custom collection state in the `sources/<source_name>` directory using `{source_name}_collection_state.go`.

## Step 3: Table

Tables are essentially the glue that binds together a source, mapper(s) & schema to establish a collection of log entries in the format of the schema, these reside in the `tables/<table_name>` directory of the plugin.

Tables **MUST** embed `table.TableImpl`, this is a generic struct parameterized with 2 types:
- The `type` of the row struct.
- The `type` of the table.

They should also implement the following functions:
- `Identifier()` returning the name of the table.
- `GetSourceMetadata()` - responsible for defining which `sources` the table supports in addition to defining the mapper associated with the source and any required source options.
- `EnrichRow` - see [enrichment](#step-5-enrichment)
- _Optionally_ `GetDescription()` to define a table description.

## Step 4: Mapping Sources to Schema

The data returned from the source may need mapping into the row struct.

Mappers reside in the `table/<table_name>` directory of the plugin, they take in data from a source and return a collection of the associated row struct.

Mappers are generic structs parameterized by row struct type.

A different mapper may be required depending on the source. It's the tables responsibility to define the related mapper in the `GetSourceMetadata()` function.

## Step 5: Enrichment

To ensure log entries are populated with the common fields (`tp_* fields`), this means that our Table needs to implement an `EnrichRow` function with the following signature, where `R` is the row struct.

```go
EnrichRow(row R, sourceEnrichmentFields schema.SourceEnrichment) (R, error)
```

The purpose of this is to populate the `enrichment.CommonFields` on our row struct, either from the source or from the existing row data.

Some of these are passed from source as `sourceEnrichmentFields` these can be set directly.

For example if we have a `ClientIP` property, this should also be added to `TpIps` and potentially `TpSourceIP` or `TPDestinationIP`.

At the very least the following fields should be set for all rows:
- `TpID` should be set to a new `xid`.
- `TpPartition` should be set to the name of the partition as defined in the HCL configuration. (Note: this needs wiring up at the moment).
- `TpIndex` should be an accountable Identifier (AWS Account ID, etc).
- `TpDate` should be a string in the format `YYYY-MM-DD`.
- `TpTimestamp` should be the timestamp associated with the log entry.
- `TpIngestTimestamp` should be the current time (ingested into tailpipe).

## Step 6: Build, Configure & Test

### Building

Run the `Makefile`:
```sh
make
```

### Configure

Create a `{pluginName}.tpc` file in the Tailpipe installations config directory (`~/.tailpipe/config`) for a partition using the new table, along with the source and any configuration options they require.

```hcl
connection "aws" "account" {
  profile = "test"
}

partition "aws_cloudtrail_log" "example" {
  source "aws_s3_bucket" {
    bucket     = "aws-cloudtrail-logs"
    connection = connection.aws.account
  }
}
```

### Testing

Run a collection on the new partition:

```sh
tailpipe collect aws_cloudtrail_log.example
```