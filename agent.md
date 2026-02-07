# Excel to Microsoft Fabric Migration Agent

## Overview

This project provides an AI-powered migration pipeline that converts Excel workbooks into a full Microsoft Fabric solution — including lakehouses, PySpark notebooks, and a Power BI semantic model. The migration preserves all inputs, logic, and outputs from the original spreadsheet, restructured into a modern medallion architecture.

## What It Does

Takes an Excel file (or its extracted XML representation) and:
1. **Analyses** the spreadsheet to extract every input, formula, and output as a human-readable Standard Operating Procedure (SOP)
2. **Designs** a Fabric medallion architecture (Bronze → Silver → Gold lakehouses with PySpark notebooks) that replicates the spreadsheet logic
3. **Deploys** the lakehouses and notebooks into a Fabric workspace
4. **Models** a Power BI semantic model with measures and relationships on the Gold layer

## How to Use

Point the agent at an Excel file or its extracted XML folder:

```
Migrate the Excel file at: input/<your-workbook-folder>/
```

To prepare an Excel file as XML:
1. Rename the `.xlsx` file to `.zip`
2. Extract the ZIP to a folder
3. Place the folder under `input/`

The orchestrator will guide you through each phase, validating with you at every step.

## Project Structure

```
exceltofabric/
├── agent.md                          # This file — project overview
├── agents/
│   ├── orchestrator.md               # Orchestrator — calls sub-agents in sequence
│   ├── 01-excel-analyzer/
│   │   └── agent.md                  # Agent 1: Parse Excel → SOP table
│   ├── 02-fabric-architect/
│   │   └── agent.md                  # Agent 2: Design Fabric architecture
│   ├── 03-fabric-deployer/
│   │   └── agent.md                  # Agent 3: Deploy into Fabric
│   └── 04-powerbi-modeller/
│       └── agent.md                  # Agent 4: Create Power BI semantic model
├── context/
│   ├── Fabric.md                     # Fabric component reference guide
│   ├── exampleSOPtableoutput.md      # Example SOP table format
│   └── fabric-configuration.json     # Workspace/lakehouse config template
├── input/                            # Place Excel files or extracted XML here
└── output/                           # Generated artefacts land here
    ├── sop-table.md                  # (generated) SOP from Agent 1
    ├── architecture.md               # (generated) Architecture from Agent 2
    ├── notebooks/                    # (generated) PySpark notebooks
    ├── fabric-items.json             # (generated) Fabric item definitions
    ├── deployment-log.md             # (generated) Deployment record
    ├── semantic-model.md             # (generated) Semantic model docs
    └── migration-summary.md          # (generated) Final summary
```

## Agent Pipeline

```
Excel/XML Input
      │
      ▼
┌─────────────────┐
│  Agent 1         │  Analyse spreadsheet, produce SOP table
│  Excel Analyzer  │  → User validates SOP
└────────┬────────┘
         ▼
┌─────────────────┐
│  Agent 2         │  Design medallion architecture + notebooks
│  Fabric Architect│  → User validates architecture + ADRs
└────────┬────────┘
         ▼
┌─────────────────┐
│  Agent 3         │  Deploy lakehouses + notebooks to Fabric
│  Fabric Deployer │  → User approves deployment
└────────┬────────┘
         ▼
┌─────────────────┐
│  Agent 4         │  Create semantic model on Gold layer
│  Power BI Model  │  → User validates model + measures
└────────┬────────┘
         ▼
   Migration Complete
```

## Required MCP Servers

- **Filesystem MCP** — reading/writing local files
- **Fabric MCP server** — creating workspaces, lakehouses, and notebooks in Fabric
- **Power BI Remote Modelling MCP server** — creating semantic models, tables, relationships, and measures

## Configuration

Edit `context/fabric-configuration.json` to set your:
- Workspace name and description
- Fabric capacity ID
- Lakehouse names and descriptions

## Key Design Principles

- **Human-in-the-loop**: Every phase has a validation gate where the user reviews and approves
- **Transparency**: All logic is explained in plain English; every architectural decision is logged as an ADR
- **Traceability**: Every measure and transformation maps back to a specific SOP step from the original Excel file
- **Simplicity**: Notebooks are kept straightforward with clear comments; no over-engineering
- **Resumability**: If interrupted, the pipeline can resume from the last completed phase
