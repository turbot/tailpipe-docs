---
title: The Basics
sidebar_label: The Basics
---

# The Basics

This guide covers the fundamental components of a Tailpipe plugin. We'll explore the boilerplate code, credentials configuration, tables, schemas, mappers, and sources.

## Plugin Structure

A typical Tailpipe plugin has the following structure:

```
tailpipe-plugin-example/
├── config/
│   └── example_connection.go
├── example/
│   └── plugin.go
├── tables/
│   └── log/
│       ├── log.go
│       ├── log_mapper.go
│       └── log_table.go
├── sources/
│   └── log_api/
│       └── log_api_source.go
├── go.mod
├── go.sum
└── main.go
```

## Boilerplate Files

### main.go

The `main.go` file is the entry point for your plugin. It handles two modes of operation:

1. **Metadata mode**: When the plugin is invoked with the `metadata` argument, it prints plugin metadata and exits.
2. **Server mode**: Otherwise, it serves the plugin using the SDK's `Serve` function.

```go
package main

import (
	"log/slog"
	"os"

	"github.com/turbot/tailpipe-plugin-example/example"
	"github.com/turbot/tailpipe-plugin-sdk/plugin"
)

func main() {
	// if the `metadata` arg was passed, we are running in metadata mode - return our metadata
	if len(os.Args) > 1 && os.Args[1] == "metadata" {
		// print the metadata and exit
		os.Exit(plugin.PrintMetadata(example.NewPlugin))
	}

	err := plugin.Serve(&plugin.ServeOpts{
		PluginFunc: example.NewPlugin,
	})

	if err != nil {
		slog.Error("Error starting plugin", "error", err)
	}
}
```

### plugin.go

The `plugin.go` file defines your plugin's structure and registers tables, sources, or formats exported. 

By convention, it should be placed in a package named after your plugin.

```go
package example

import (
	"github.com/turbot/go-kit/helpers"
	"github.com/turbot/tailpipe-plugin-example/config"
	"github.com/turbot/tailpipe-plugin-example/tables/log"
	"github.com/turbot/tailpipe-plugin-sdk/plugin"
	"github.com/turbot/tailpipe-plugin-sdk/row_source"
	"github.com/turbot/tailpipe-plugin-sdk/table"
)

type Plugin struct {
	plugin.PluginImpl
}

func init() {
	// Register tables, with type parameters:
	// 1. row struct
	// 2. table implementation
	table.RegisterTable[*log.Log, *log.LogTable]()

	// Register sources if needed
	row_source.RegisterRowSource[*log_api.LogAPISource]()
}

func NewPlugin() (_ plugin.TailpipePlugin, err error) {
	defer func() {
		if r := recover(); r != nil {
			err = helpers.ToError(r)
		}
	}()

	p := &Plugin{
		PluginImpl: plugin.NewPluginImpl(config.PluginName),
	}

	return p, nil
}
```

## Credentials Configuration

The credentials configuration defines how users connect to your plugin's data source. It's typically defined in a file like `config/example_connection.go`.

> Note: Not all sources require credentials. If your source doesn't require credentials, you can omit defining a connection.

```go
package config

import (
	"fmt"
)

const PluginName = "example"

type ExampleConnection struct {
	// Define connection parameters with JSON and HCL tags
	Token     string `json:"token" hcl:"token"`
}

func (c *ExampleConnection) Validate() error {
	// Validate required fields
	if c.Token == "" {
		return fmt.Errorf("token is required")
	}
	return nil
}

func (c *ExampleConnection) Identifier() string {
	return PluginName
}
```


## Tables

Tables define the structure of your data and how it's presented to users. Each table typically consists of three components:

1. **Row struct**: Defines the schema of a static table, custom tables define their own schema.
2. **Table implementation**: Defines table metadata and behavior.
3. **Mapper**: Maps data from your source to the row struct / table schema.

### Table Schema (Row Struct)

The row struct defines the schema of your table. It's typically defined in a file like `tables/log/log.go`.

> Note: It should embed `schema.CommonFields` to include standard fields required by Tailpipe.

```go
package log

import (
	"time"

	"github.com/turbot/tailpipe-plugin-sdk/schema"
)

// Log is the struct containing the enriched data for a log record
type Log struct {
	// Embed required enrichment fields
	schema.CommonFields

	// Define your table's columns
	Id          string                 `json:"id"`
	Message     string                 `json:"message"`
	Level       string                 `json:"level"`
	Timestamp   time.Time              `json:"timestamp"`
	Metadata    map[string]interface{} `json:"metadata" parquet:"type=JSON"`
	ServiceName string                 `json:"service_name"`
}

// GetColumnDescriptions returns a map of column names to their descriptions
func (l *Log) GetColumnDescriptions() map[string]string {
	return map[string]string{
		"id":           "The unique identifier of the log entry.",
		"message":      "The log message.",
		"level":        "The log level (e.g., INFO, ERROR, DEBUG).",
		"timestamp":    "The date and time when the log entry was created.",
		"metadata":     "Additional metadata related to the log entry, in JSON format.",
		"service_name": "The name of the service that generated the log.",
	}
}
```

### Table Implementation

The table implementation defines metadata about your table and how it behaves. It's typically defined in a file like `tables/log/log_table.go`.

```go
package log

import (
	"time"

	"github.com/rs/xid"

	"github.com/turbot/tailpipe-plugin-example/sources/log_api"
	"github.com/turbot/tailpipe-plugin-sdk/schema"
	"github.com/turbot/tailpipe-plugin-sdk/table"
)

const LogTableIdentifier = "example_log"

type LogTable struct {
}

func (t *LogTable) Identifier() string {
	return LogTableIdentifier
}

func (t *LogTable) GetSourceMetadata() ([]*table.SourceMetadata[*Log], error) {
	return []*table.SourceMetadata[*Log]{
		{
			SourceName: log_api.LogAPISourceIdentifier,
			Mapper:     NewLogMapper(),
		},
	}, nil
}

func (t *LogTable) EnrichRow(row *Log, sourceEnrichmentFields schema.SourceEnrichment) (*Log, error) {
	// Add common fields from the source
	row.CommonFields = sourceEnrichmentFields.CommonFields

	// Add additional fields
	row.TpID = xid.New().String()
	row.TpDate = row.Timestamp.Truncate(24 * time.Hour)
	row.TpTimestamp = row.Timestamp
	row.TpIngestTimestamp = time.Now()

	return row, nil
}

func (t *LogTable) GetDescription() string {
	return "Example logs detail application events and activities."
}
```

## Mapper

The mapper converts data from your source format to your table's row struct. It's typically defined in a file like `tables/log/log_mapper.go`.

> **Note**: A mapper is only required if you need to map data from the source to the row struct. If your source already provides data in the format of your row struct, or you have an alternative approach (`extractor`/`formatter`) you may not need a `mapper`.

```go
package log

import (
	"context"
	"log/slog"
	"time"

	"github.com/turbot/tailpipe-plugin-sdk/error_types"
	"github.com/turbot/tailpipe-plugin-sdk/mappers"
)

type LogMapper struct {
}

func (m *LogMapper) Map(_ context.Context, data any, _ ...mappers.MapOption[*Log]) (*Log, error) {
	// Type assertion to convert the generic data to your source's format
	input, ok := data.(YourSourceLogFormat)
	if !ok {
		slog.Error("unable to map log record: expected YourSourceLogFormat, got %T", data)
		return nil, error_types.NewRowErrorWithMessage("unable to map row, invalid type received")
	}

	// Create a new log struct
	log := &Log{}

	// Map fields from the source format to your row struct
	log.Id = input.ID
	log.Message = input.Text
	log.Level = input.Severity
	log.ServiceName = input.Service

	// Parse timestamp
	timestamp, err := time.Parse(time.RFC3339, input.Time)
	if err != nil {
		slog.Error("log %s failed mapping timestamp field to time.Time: %v", input.ID, err)
		return nil, error_types.NewRowErrorWithFields([]string{}, []string{"timestamp"})
	}
	log.Timestamp = timestamp

	// Map metadata
	log.Metadata = input.AdditionalData

	return log, nil
}

func (m *LogMapper) Identifier() string {
	return "example_log_mapper"
}
```

## Source

A source is responsible for collecting data from an external system. It's typically defined in a file like `sources/log_api/log_api_source.go`.

> **Note**: A source is only required if the SDK or another existing plugin doesn't already provide a source for your data. For example, if you're using a standard API or file format that's already supported, you may not need to implement your own source.

```go
package log_api

import (
	"context"
	"fmt"
	"log/slog"
	"time"

	"github.com/turbot/tailpipe-plugin-example/config"
	"github.com/turbot/tailpipe-plugin-sdk/collection_state"
	"github.com/turbot/tailpipe-plugin-sdk/row_source"
	"github.com/turbot/tailpipe-plugin-sdk/schema"
	"github.com/turbot/tailpipe-plugin-sdk/types"
)

const LogAPISourceIdentifier = "example_log_api"

// LogAPISource is responsible for collecting logs from your API
type LogAPISource struct {
	row_source.RowSourceImpl[*LogAPISourceConfig, *config.ExampleConnection]

	// Optional: Use a specialized collection state
	CollectionState *collection_state.ReverseOrderCollectionState[*LogAPISourceConfig]
}

func (s *LogAPISource) Init(ctx context.Context, params *row_source.RowSourceParams, opts ...row_source.RowSourceOption) error {
	// Set up collection state if needed
	s.NewCollectionStateFunc = collection_state.NewReverseOrderCollectionState

	// Call base init
	if err := s.RowSourceImpl.Init(ctx, params, opts...); err != nil {
		return err
	}

	// Type assertion for collection state if needed
	s.CollectionState = s.RowSourceImpl.CollectionState.(*collection_state.ReverseOrderCollectionState[*LogAPISourceConfig])
	return nil
}

func (s *LogAPISource) Identifier() string {
	return LogAPISourceIdentifier
}

func (s *LogAPISource) Collect(ctx context.Context) error {
	// Start collection
	s.CollectionState.Start()
	defer s.CollectionState.End()

	// Create API client using connection details
	client := createYourAPIClient(s.Connection.Token)

	// Set up source enrichment fields
	sourceName := LogAPISourceIdentifier
	sourceEnrichmentFields := &schema.SourceEnrichment{
		CommonFields: schema.CommonFields{
			TpSourceName:     &sourceName,
			TpSourceType:     LogAPISourceIdentifier,
			TpSourceLocation: &s.Connection.OrgHandle,
		},
	}

	// Fetch and process data
	logs, err := client.FetchLogs(ctx)
	if err != nil {
		return fmt.Errorf("error fetching logs: %v", err)
	}

	for _, log := range logs {
		// Check if we should collect this item
		logTime := log.Timestamp
		if logTime.Before(s.FromTime) || !s.CollectionState.ShouldCollect(log.ID, logTime) {
			continue
		}

		// Build a row from the log and collect it
		row := &types.RowData{Data: log, SourceEnrichment: sourceEnrichmentFields}
		if err = s.CollectionState.OnCollected(log.ID, logTime); err != nil {
			return fmt.Errorf("error updating collection state: %w", err)
		}
		if err = s.OnRow(ctx, row); err != nil {
			return fmt.Errorf("error processing row: %w", err)
		}
	}

	return nil
}
```

## Conclusion

This guide covered the basic components of a Tailpipe plugin:

- **Boilerplate files**: `main.go` and `plugin.go`
- **Credentials configuration**: How to define and validate connection parameters
- **Tables**: How to define table metadata and behavior
- **Table schemas**: How to define the structure of your data
- **Mappers**: How to convert data from your source format to your table's row struct
- **Sources**: How to collect data from external systems

By understanding these components, you can create your own Tailpipe plugin to integrate with any data source.

For more detailed information and examples, refer to the [Tailpipe Plugin SDK](https://github.com/turbot/tailpipe-plugin-sdk) and existing plugins like [tailpipe-plugin-pipes](https://github.com/turbot/tailpipe-plugin-pipes), [tailpipe-plugin-aws](https://github.com/turbot/tailpipe-plugin-aws) or for examples on advanced topics like pre-defined custom tables [tailpipe-plugin-nginx](https://github.com/turbot/tailipe-plugin-nginx).