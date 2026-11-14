# Theme Studio — 35-minute Microsoft Reactor demo

## Goal

Show how Copilot Studio, Power Automate, and Dataverse can work together to guide a
shopper from event requirements to a priced, curated shopping plan and save it only after
explicit approval.

The existing `EventPlanner` solution is already available in the Power Platform
environment. **Do not rebuild or re-import it for this demo.** Use the deployed agent,
flows, and Dataverse data as the source of truth. Repository files are reference material,
not a tenant export; topic YAML files may contain placeholder flow IDs.

The Dataverse tables already available in the solution can be reused with **Add existing**.
Do not create duplicate tables or re-import the sample CSVs for a new demo in the same
environment.

## 35-minute run of show

Plan for about 30 minutes of presentation and 5 minutes of audience questions.

| Time | Segment | What to show |
| --- | --- | --- |
| 0–3 min | Welcome and scenario | Introduce the problem: turning a shopper's occasion, guest count, style, and budget into a useful shopping plan. State that this is a demo/quote workflow, not checkout or payment. |
| 3–6 min | Architecture | Show the existing `EventPlanner` solution and explain the path: Copilot Studio conversation → Power Automate orchestration/calculation → Dataverse catalog and saved plans. |
| 6–9 min | Explain the catalog | In Dataverse, briefly show Themes, Products, and Theme Products. Explain that the theme-product mapping is the curated boundary: recommendations should come from the selected theme's mapped products, not invented catalog items. |
| 9–21 min | Live shopper journey | Use the tested conversation below. Pause to explain discovery, theme matching, flow-backed pricing, budget trimming, and explicit approval. |
| 21–25 min | Change one requirement | Ask to change the budget or guest count. Show the plan recalculating from the same selected theme rather than starting the whole conversation again. |
| 25–29 min | Persist and verify | Approve saving, then show the new Event Plan and its Event Plan Lines in Dataverse. Point out the plan number, total, guest count, included state, and saved line records. |
| 29–30 min | Takeaways | Recap trusted data, deterministic calculations, a human confirmation point, and a useful Dataverse record. |
| 30–35 min | Audience Q&A | Take questions. If there are no questions, show a prepared second test or discuss production hardening. |

## Before the session

Do this in the same environment and tenant you intend to present from.

### The day before

- Confirm you can sign in to the target Power Platform environment and open the
  `EventPlanner` solution.
- If assembling a separate demo solution, use **Solutions → [your demo solution] → Add
  existing → Table** to add the existing Theme, Product, Theme Product, Event Plan, and
  Event Plan Line tables. Select the existing Dataverse tables; do not create similarly
  named replacements. Add only the tables the demo needs, and check for required
  components/dependencies when prompted.
- Reuse the existing table data in that environment. Do not import the sample CSVs again
  unless you intentionally chose a clean environment and verified it has no demo data.
- For the fastest Reactor presentation, stay in the existing `EventPlanner` solution and
  show its tables directly; making a second solution is optional, not a demo prerequisite.
- Open the existing Theme Studio agent in Copilot Studio. Use its existing connected
  actions; **do not paste the repository YAML over the working topics**.
- Confirm the four expected flows are present, turned on, and connected successfully:
  `GetThemes`, `GetThemeDetails`, `BuildEventPlan`, and `SaveEventPlan`.
- Check that Dataverse has active themes, in-stock products, and Theme Product mappings.
  Verify the Tropical Luau theme (`TROP`) is active and its expected mapped products are
  available.
- Run the full demo once in the Copilot Studio test pane. Confirm the displayed result
  and saved records in the live environment; do not rely on figures in source files
  without checking them against the deployed flows and data.
- Check that the agent is published if you will use a channel rather than the Copilot
  Studio test pane. Prefer the test pane for a controlled presenter demo.
- Have the solution page, agent test pane, and Dataverse Event Plans view open in separate
  browser tabs. Sign in and select the correct environment before the audience arrives.
- Use demo records and an approved demo account only. Avoid exposing customer data,
  connection details, environment URLs, or credentials on screen.

### Ten minutes before

- Start a fresh test conversation so no previous variables or selected theme affect the
  run.
- Confirm the browser is sharing the intended window/tab, zoom is readable, notifications
  are silenced, and the network is stable.
- Keep a pre-run screenshot or short screen recording of a successful conversation as a
  backup. Do not use it to imply a failed live run succeeded.
- If the save action writes a record, use the dedicated demo environment and tell the
  audience it creates a demo Event Plan. Do not delete records during the presentation.

## Main live script

Use the following path because it demonstrates a real business decision rather than
only showing a happy-path chat:

1. Start a new conversation and enter: **“Plan a birthday for 40 people.”**
2. Answer the discovery prompts:
   - Occasion: **Birthday**
   - Guests: **40**
   - Vibe: **Bright & Tropical**
   - Budget: **400**
3. Select **Tropical Luau** from the returned recommendations.
4. Explain that the agent is retrieving the selected theme's mapped products and asking
   the flow to calculate quantities and prices. Point out the included total and any
   optional items removed to meet the budget.
5. If the environment returns the expected tested data, call out the reference figures:
   **£379.82** included total, **£24.91** essential/core amount above a £200 budget in the
   separate shortfall test, **3.5 hours** setup, and the Neon LED Sign excluded at a £400
   cap. For the £400 path, the expected sample result is **11 included lines**; confirm
   these exact numbers in preflight before quoting them.
6. Ask the agent to adjust the budget (for example, **“Change my budget to £600”**).
   Point out that the same theme is repriced. If the deployed topic triggers differently,
   use the wording you verified during rehearsal.
7. Return to a budget of £400 if needed, then choose **Yes, create my order** only after
   the displayed amount is confirmed. Make explicit that the demo saves an Event Plan; it
   does not charge a card or place a supplier order.
8. Switch to Dataverse and open the newly created Event Plan. Show its plan number,
   status, amount, and related Event Plan Lines. Verify the saved line count against the
   included products shown in the conversation.

### Presenter narration

- **During discovery:** “The agent gathers structured answers, not just free-form chat.
  Those values drive the recommendation.”
- **At theme selection:** “A theme is a curated assortment. The model can explain it, but
  it should not invent products or prices.”
- **At the calculation:** “Power Automate is doing the repeatable work: retrieving the
  mapped items, calculating quantities and totals, and applying the budget rules.”
- **At the budget trim:** “Core items are protected. The plan is transparent about what
  was excluded rather than quietly claiming the budget was met.”
- **Before save:** “The user sees the amount and must approve it. That confirmation is a
  deliberate boundary before creating a record.”
- **In Dataverse:** “The conversation has produced business data we can inspect and
  follow up on—not just a chat transcript.”

## Optional branch if time allows

Use one branch, not all of them:

- **Theme details:** Ask for more information about the selected theme. Show the
  description, palette, mapped core items, upgrades, or styling tips if the deployed
  agent returns them correctly.
- **Budget shortfall:** In a fresh conversation, test a very low budget. Explain that an
  honest shortfall is better than pretending essentials fit. Use the live result rather
  than quoting a precomputed number.
- **Stock availability:** Only demonstrate this if you have rehearsed with a safe,
  reversible demo-data change and restored it afterward. Otherwise explain the behavior
  without editing stock live.

Do not promise a party-time schedule in this demo unless you have confirmed that the
deployed agent actually offers and generates one. The repository instructions describe
some planned behavior that is not clearly wired into the checked-in topic flow.

## Recovery plan

| If this happens | Do this |
| --- | --- |
| The agent asks an unexpected question | Say: “Let me reset to the prepared scenario.” Start a fresh test conversation and use the scripted answers. |
| No themes appear | Do not change data live. Show the pre-run screenshot, explain the expected flow, and inspect environment, active-theme data, and flow status after the session. |
| A flow errors or times out | Do not claim success. Switch to the pre-run capture, explain that the live integration needs investigation, and continue with the architecture/Dataverse walkthrough. |
| The total differs from the rehearsed result | Use the live result as authoritative only after checking the selected theme, guest count, budget, stock, and data. Otherwise stop before save and use the recovery path. |
| The save succeeds but the record is hard to find | Search the Event Plans table by the plan number returned in chat. If it is still unavailable, do not repeat the save blindly; verify whether a record was created. |
| Network or sign-in fails | Use the prepared recording/screenshots and describe the intended live steps honestly. Do not expose credentials or try an unapproved tenant. |

## Audience questions to invite

- Why use a curated Theme Product mapping instead of asking a generative model to choose
  products?
- Which calculations belong in a flow rather than in the conversation?
- How would you add a payment or inventory-reservation step safely?
- What would need to change for production: authentication, privacy, permissions,
  connection ownership, monitoring, and failure handling?

## Scope and caveats

- This is a conversational planning and demo-order workflow, not payment, inventory
  reservation, fulfilment, or a production commerce integration.
- The repository contains Dataverse schema/data references and Copilot Studio source. It
  does not include the live tenant configuration or importable Power Automate definitions.
- Deployed flow behavior, data, agent publication, and exact results must be verified in
  the target environment before presenting.
