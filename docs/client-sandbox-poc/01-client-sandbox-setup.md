
## Step 3 — Create Dataverse choices

1. In the solution select **New → More → Choice**.
2. Follow [choices.md](../../dataverse/schema/choices.md) to create the seven global
   choices: Occasion, Vibe, Category, Difficulty, Role, Quantity Basis, and Plan Status.
3. Enter the explicit option values shown in the schema. Do not let Dataverse assign
   different values; filters and flow logic rely on them.
4. Save and publish the customizations.

**Done when:** all seven global choices exist in the solution and their labels and
numeric values match the schema file.

## Step 4 — Create Dataverse tables and relationships

1. Create tables in this order from
   [tables.md](../../dataverse/schema/tables.md): Theme, Product, Theme Product, Event
   Plan, Event Plan Line.
2. For each table, configure the primary name column and all other columns exactly as
   documented, including data types, requiredness, currency, choice, and autonumber
   settings.
3. Add the documented alternate keys for Theme Code and SKU.
4. Add the five documented relationships, paying particular attention to the parental
   Event Plan → Event Plan Line relationship.
5. Create the recommended views if needed for client review and manual QA.
6. Publish all customizations.

**Done when:** each table opens without errors, lookup/choice columns resolve correctly,
and the relationships and keys show as active.

For detailed column and relationship definitions, use
[Dataverse setup](../02-dataverse-setup.md).

## Step 5 — Load POC catalog data

Use sample data for a disposable demonstration, or have the client approve and provide
the content before substituting it. Do not treat the sample catalog as the client's
real assortment or pricing.

1. Import `themes.csv` into Theme.
2. Import `products.csv` into Product.
3. Import `theme-products.csv` into Theme Product after both parent tables are loaded.
4. During Theme Product import, map `ThemeName` and `ProductName` to their lookups and
   ignore the helper column `ThemeCode`.
5. Map Choice values by their labels as described in
   [Dataverse setup](../02-dataverse-setup.md).
6. Review the import job for failures. Fix unresolved lookups rather than leaving
   orphaned mappings.
7. Verify the sample counts only when using the repository sample files: 8 themes, 58
   products, and 96 mappings.

**Done when:** imported rows have the expected lookups, active flags, prices, and stock
values, and no Theme Product row has a blank Theme or Product lookup.

## Step 6 — Create the flow security role

1. In the solution, create **EventPlanner — Agent** using the privilege matrix in
   [Dataverse setup](../02-dataverse-setup.md).
2. Ask the client admin to assign the role to the approved identity used by the flow
   connection.
3. Verify that this identity can read Theme, Product, and Theme Product, and create the
   Event Plan and Event Plan Line records required by the POC.
4. Verify it cannot change the reference catalog unless the client explicitly approved
   that permission.

**Done when:** the flow identity's role is assigned and the permissions have been
checked with a non-production test.

## Step 7 — Build and test the four flows

Build each flow **inside the EventPlanner solution**, using its linked specification.
Use the Copilot Studio/Power Virtual Agents call trigger and ensure each path reaches a
response action.

1. Build **GetThemes** from
   [01-GetThemes.md](../../power-automate/01-GetThemes.md).
2. Build **GetThemeDetails** from
   [02-GetThemeDetails.md](../../power-automate/02-GetThemeDetails.md).
3. Build **BuildEventPlan** from
   [03-BuildEventPlan.md](../../power-automate/03-BuildEventPlan.md).
4. Build **SaveEventPlan** from
   [04-SaveEventPlan.md](../../power-automate/04-SaveEventPlan.md).
5. Use a client-approved Dataverse connection and solution connection references where
   available. Do not paste credentials into the flow or the repository.
6. Match each trigger input's name, type, and order to the flow specification. Bind
   inputs positionally by type exactly as documented.
7. Test each flow manually before attaching it to the agent. In particular test
   `BuildEventPlan` with Theme Code `TROP`, 40 guests, Table Count `0`, and Budget `400`.
   For the repository sample catalog expect total `379.82`, trimmed count `1`, and setup
   hours `3.5`.
8. Test `SaveEventPlan` with a test-only plan and verify the parent and line rows. Delete
   test records only if the client approves and the cleanup method is understood.
9. Turn on all four flows after their manual tests pass.

**Done when:** all four flows run successfully in the sandbox, return the documented
outputs, and use the intended connection.

Detailed build conventions are in [03-flows-setup.md](../03-flows-setup.md).

## Step 8 — Create and configure the agent

1. Open [copilotstudio.microsoft.com](https://copilotstudio.microsoft.com) and confirm
   it is in the same client sandbox as the EventPlanner solution.
2. Create an agent named **Theme Studio** (or the client-approved demo name).
3. Add/move it into the EventPlanner solution using the current Copilot Studio solution
   workflow.
4. Apply the instructions from
   [agent-instructions.md](../../copilot-studio/agent-instructions.md).
5. Use the orchestration mode and model approved for this POC, and keep web search and
   unsupported general knowledge off for catalog claims.
6. Add Theme, Product, and Theme Product as knowledge sources if needed. Do **not** add
   Event Plan or Event Plan Line as agent knowledge sources.
7. Create the documented global variables, or allow the topic import to create them,
   then verify their types.

**Done when:** the agent is in the correct sandbox solution, its instructions are saved,
and the catalog knowledge sources are limited to approved reference data.

## Step 9 — Add topics and connect flows

1. Open **Topics** in the agent.
2. Add topics from
   [copilot-studio/topics](../../copilot-studio/topics) one at a time, using the
   instructions in [04-copilot-studio-setup.md](../04-copilot-studio-setup.md).
3. Replace the built-in greeting/conversation-start and fallback topics only as described
   by that setup guide.
4. For each `BeginDialog` reference, select the destination topic in the designer so
   Copilot Studio records the actual topic name generated in this client's environment.
5. For each flow action, select the flow built in Step 7 and map all inputs and outputs.
   Do not leave placeholder flow IDs or unresolved red nodes.
6. Save each topic. Review authoring errors and resolve them before continuing.

**Done when:** every topic is enabled as intended, has no unresolved errors, and calls
the correct sandbox flow.

## Step 10 — Test the whole demo

Use the test pane. With repository sample data:

1. Start with `Plan a birthday for 40 people`.
2. Select Birthday, 40 guests, Bright & Tropical, and budget `400` when prompted.
3. Select **Tropical Luau**.
4. Check the calculated total is **£379.82**, setup time is **3.5 hours**, and the Neon
   LED Sign appears as the excluded optional item.
5. Choose **Yes, create my order** only after confirming the displayed total.
6. Confirm one Event Plan parent and the expected Event Plan Lines were created. With
   the sample plan, 12 mapped lines are saved, including the excluded line marked
   `Included = No`; the included total should still be £379.82.
7. Start a new test session and check that choosing **Not yet** does not create an order.
8. Run the other happy-path, budget, stock, adjustment, and guardrail checks in
   [05-testing-and-publishing.md](../05-testing-and-publishing.md).

Use only test data. If a test writes records, follow the agreed cleanup procedure.

**Done when:** the client business contact has reviewed the responses and totals, the
Dataverse rows match the conversation, and all failed tests are either fixed or recorded
as known POC limitations.

## Step 11 — Publish to the sandbox demo channel

1. Save all topics and flow changes.
2. Publish the agent in Copilot Studio.
3. Enable/use the sandbox demo channel selected with the client (for example, the demo
   website) and share it only with the agreed audience.
4. Test the published channel, not only the maker test pane.
5. Confirm with the client that users understand the POC does not take payment or
   confirm fulfilment.

**Done when:** the agent is published in the correct sandbox and the client can run the
accepted demonstration.

## Step 12 — Handover and support

Give the client contact:

- Sandbox environment name and ID.
- Solution name and version, plus where the client keeps the source files.
- Agent name and demo channel URL, shared through an approved channel.
- Flow names, owners, connection-reference owners, and who renews/replaces connections.
- Which catalog data was loaded, its source, and how to change prices/stock safely.
- Test results, known limitations, and unresolved POC issues.
- How to find a saved plan by its plan number and where flow run errors are reviewed.
- Named business, maker, admin, and support contacts.
- Agreed cleanup date and instructions for disabling the channel and removing test data.

**Done when:** the client accepts the handover, knows whom to contact, and owns access,
connections, data, and any ongoing support for the sandbox.

## Common POC issues

| Symptom | First check |
| --- | --- |
| No themes are returned | GetThemes flow is on; occasion/vibe labels and explicit choice values match; sample Theme rows are active. |
| A theme has missing products | Theme Product imports resolved both lookups; products are active and in stock. |
| Flow action is red or unavailable | Flow is in the same solution/environment, on, and reselected in the topic; connection is approved and healthy. |
| Agent asks for name/email/date | Check both agent instructions and the Save And Share topic/tool description; republish after edits. |
| Displayed total is wrong | Test BuildEventPlan manually and inspect quantity basis, stock, unit prices, and budget trimming. |
| Parent plan exists but lines are missing | Inspect SaveEventPlan run history, Dataverse append privileges, and every Apply to each action. |
| Changes do not show in the channel | Save the topic, publish the agent, then start a new channel conversation/session. |
