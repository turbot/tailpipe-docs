---
title: Add Detection to Mod
sidebar_label: Add Detection to Mod
---

# Add Detection to Mod

When adding detections to an existing mod, we have included some prompts below that you can use with your AI tools.

## Adding a new detection to a mod

> Note: You may need to amend the URL in Step 1 of the below prompt to be more akin to your mod

```md
---
# Specify the following for Cursor rules
description: Guidelines for creating a new detection
alwaysApply: false
---

# Adding Tailpipe mod detections

You are an expert in [Tailpipe](https://tailpipe.io) CLI, plugins, mods and [DuckDB] (https://duckdb.org/).

## 1. Obtain conventions
- **ALWAYS** obtain conventions from the current project AND the sample repository: https://github.com/turbot/tailpipe-mod-nginx-access-log-detections focus on:
    - Naming conventions
    - Directory structures
    - Code structure
    - Query structure and conventions
- **ALWAYS** repeat the conventions you understand

## 2. Create the Query
- **ALWAYS** follow conventions for query structure **MUST** match existing query structure and layout based on conventions
- **MUST** be DuckDB syntax compliant
- **ALWAYS** test the SQL Query by either of the following options before preparing the next step
    * Tailpipe MCP Tool `tailpipe_query` **Preferred**
    * Tailpipe Query CLI command
- **MUST** NOT proceed beyond this step without having a working and tested query
- When you have a working query, give me a summary of the results

## 3. Validate Query
- **ALWAYS** validate query matches style of existing queries from the current project or the conventions obtained from Step 1.

## 4. Detection
- Detection should be created in the directory/file most suitable based on conventions

### 4a. Variables
- **MUST** Place non-shared variables in the same file as the matching control
- **MUST** Only use variables.pp for new SHARED variables
- **MUST** Ensure we have a database variable in `variables.pp`
    - **DO NOT** Re-create or modify this is it already exists

## 5. Documentation
- **MUST** Create or amending existing documentation in the location/directory most suitable based on conventions
- IF existing documentation is present, utilise this directory/file.

## 6. Miscellaneous Updates
- **DO NOT** update the `README.md` unless absolutely required

## 7. Testing
- **ALWAYS** test the detection with either of the following, iterate any fixes required as this **MUST** test successfully
    * Powerpipe MCP tool `powerpipe_detection_run` **Preferred**
        * Ensure to update the `mod-location` to current working directory
        * If errors on qualified_name, use unqualified name
    * Powerpipe CLI command `powerpipe detection run X`

## 8. Dashboards
- **MUST** Determine if any updates to the Dashboards make sense, if any changes are required **PLAN** these changes and **PROMPT** for permission before applying

## 9. Complete
- Summarise all steps undertaken, providing a reasonable but not overly verbose description of all steps and the outcome.
```

## Obtain potential new detections

```md
# Obtaining potential missing detections for current Tailpipe mod

You are an expert in [Tailpipe](https://tailpipe.io) CLI, plugins, mods and [DuckDB] (https://duckdb.org/).

## 1. Obtain Context
- **ALWAYS** read the `README.md` to establish the context of the mod
- This should provide clarity on what the detections within the mod aim to achieve.
- Repeat your understanding of the mods purpose.

## 2. Identify potential missing detections
- **ALWAYS** Check to see if a detection satisfies the purpose of the mod
- **ALWAYS** Ensure that a detection doesn't already exist that satisfies the requirements
- List the potential options back to me so I can see what needs implementing
  - Prioritize those which have a higher risk rating
```