---
title: AI Tools (MCP)
---

# AI Tools (MCP)

The [Tailpipe MCP server](https://github.com/turbot/tailpipe-mcp) (Model Control Protocol) transforms how you interact with your cloud and security data.  It brings the power of conversational AI to your security and cloud logs, allowing you to extract critical insights using plain English — no complex SQL required!

The Tailpipe [MCP](https://modelcontextprotocol.io/introduction) enables Large Language Models (LLMs) to query your Tailpipe data directly. This allows you to interact with your security and cloud logs using natural language, making data exploration and analysis more intuitive and accessible to a wider audience.

The MCP is packaged separately and runs as an integration in your AI tool, such as [Claude Desktop](https://claude.ai/download) or [Cursor](https://www.cursor.com/).

## Installation

### Prerequisites

- [Tailpipe](https://tailpipe.io/downloads) installed and configured
- [Node.js](https://nodejs.org/) v16 or higher (includes `npx`)
- An AI assistant that supports [MCP](https://modelcontextprotocol.io/introduction), such as [Cursor](https://www.cursor.com/) or Anthropic's [Claude Desktop](https://claude.ai/download).

### Configuration

The Tailpipe MCP server is packaged and distributed as an NPM package; just add Tailpipe MCP to your AI assistant's configuration file and restart your AI assistant for the changes to take effect:

```json
{
  "mcpServers": {
    "tailpipe": {
      "command": "npx",
      "args": [
        "-y",
        "@turbot/tailpipe-mcp"
      ]
    }
  }
}
```


| Assistant | Config File Location | Setup Guide |
|-----------|---------------------|-------------|
| Claude Desktop | `claude_desktop_config.json` | [Claude Desktop MCP Guide →](https://modelcontextprotocol.io/quickstart/user) |
| Cursor | `~/.cursor/mcp.json` | [Cursor MCP Guide →](https://docs.cursor.com/context/model-context-protocol) |

Refer to the [README](https://github.com/turbot/tailpipe-mcp/blob/main/README.md) for additional configuration options.


## Querying Tailpipe

To query Tailpipe, just ask questions using natural language!



Explore your log data:
```
What tables do we have available in Tailpipe?
```

Simple, specific questions work well:
```
Show me all S3 bucket creation events from CloudTrail in the last 24 hours
```

Dive into incident analysis:
```
Find any IAM users created outside working hours (9am-5pm) in the last week
```

Get timeline insights:
```
Give me a timeline of the session where Venu created the IAM access key
```

Explore potential risks:
```
Analyze our cloudtrail errors for any specific security risks
```

## Best Practices for Prompts

To get the most accurate and helpful responses from the MCP service, consider these best practices when formulating your prompts:

1. **Use natural language**: The LLM will handle the SQL translation
2. **Be specific**: Include relevant time frames, resource types, and conditions
3. **Include context**: Mention the type of data you want to analyze (CloudTrail events, cost data, etc.)
4. **Ask for explanations**: Request the LLM to explain its findings after presenting the data
5. **Iterative refinement**: Start with simple queries and then refine based on initial results
6. **Be bold and exploratory**:  It's amazing what the LLM will discover and achieve!

## Limitations

- The quality of SQL generation depends on the LLM's understanding of your prompt and the Tailpipe schema.
- Complex analytical queries may require iterative refinement.
- Response times depend on both the LLM API latency and query execution time.
- The MCP server only runs locally at this time.  You must run it from the same machine as your AI assistant.
- A valid subscription to the LLM provider is recommended; free plan limits are often insufficient for using the Tailpipe MCP server.