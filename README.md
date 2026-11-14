# Theme Studio — DIY Event Planner

Theme Studio is a Copilot Studio agent for planning DIY events. It recommends a theme,
builds a priced shopping plan from the products mapped to that theme, and can save an
approved plan and its line items to Dataverse.

## Solution flow

```text
Shopper
  → Copilot Studio topics
  → Power Automate flows
  → Dataverse
      Themes, Products, Theme Products
      Event Plans, Event Plan Lines
```

The topics guide discovery, recommendations, plan building and adjustments, theme
details, saving, and fallback handling. The flows read the catalog, calculate quantities
and totals, apply budget trimming without removing core items, and persist approved plans.

## Repository contents

| Path | Contents |
| --- | --- |
| [dataverse/schema/](./dataverse/schema) | Dataverse table definitions and global choice values |
| [dataverse/sample-data/](./dataverse/sample-data) | CSV data for themes, products, and theme-product mappings |
| [copilot-studio/](./copilot-studio) | Agent instructions and the seven topic YAML source files |
| [docs/microsoft-reactor-35-minute-demo.md](./docs/microsoft-reactor-35-minute-demo.md) | Presenter runbook for a 35-minute Microsoft Reactor session |

The publisher prefix used by the schema is `stw`; the solution name referenced by the
source files is `EventPlanner`.

## Runtime and source-of-truth

The connected Power Platform environment is the runtime source of truth. This repository
contains the topic source and Dataverse schema/data references. It does not contain
Power Automate flow definitions or a Power Platform solution export, and it does not
connect to or verify a tenant. The flows used by the connected agent live in the Power
Platform environment.

This demo saves an approved plan but does not take payment, reserve stock, or place a
supplier order.
