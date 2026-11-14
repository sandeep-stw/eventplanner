# Dataverse schema — global choices

Create these as **global choices** in the `EventPlanner` solution so Copilot Studio,
the flows, and the CSV imports all agree on the same values.

The numeric values below matter: the sample CSVs and the flow filters reference them
directly. If you let Dataverse auto-generate values instead, update the CSVs and the
corresponding filters in the Power Automate flows to match.

---

## `stw_occasion` — Occasion

| Label | Value |
| --- | --- |
| Birthday | 100000000 |
| Wedding / Engagement | 100000001 |
| Baby Shower | 100000002 |
| Holiday Party | 100000003 |
| Graduation | 100000004 |
| Anniversary | 100000005 |
| Corporate / Office | 100000006 |
| Just Because | 100000007 |

## `stw_vibe` — Vibe

| Label | Value |
| --- | --- |
| Bright & Tropical | 100000100 |
| Soft & Boho | 100000101 |
| Elegant & Glam | 100000102 |
| Cozy & Rustic | 100000103 |
| Bold & Neon | 100000104 |
| Sweet & Pastel | 100000105 |
| Icy & Wintry | 100000106 |
| Dark & Spooky | 100000107 |

## `stw_category` — Product Category

| Label | Value |
| --- | --- |
| Balloons | 100000200 |
| Backdrops | 100000201 |
| Tableware | 100000202 |
| Lighting | 100000203 |
| Signage | 100000204 |
| Florals & Greenery | 100000205 |
| Favors | 100000206 |
| Craft Tools | 100000207 |

## `stw_difficulty` — DIY Difficulty

| Label | Value |
| --- | --- |
| Easy | 100000300 |
| Moderate | 100000301 |
| Ambitious | 100000302 |

## `stw_role` — Item Role

Drives budget trimming: **Core** items are never removed, **Accent** items are removed
second, **Upgrade** items are removed first.

| Label | Value |
| --- | --- |
| Core | 100000400 |
| Accent | 100000401 |
| Upgrade | 100000402 |

## `stw_quantitybasis` — Quantity Basis

| Label | Value | Quantity formula |
| --- | --- | --- |
| Per Event | 100000500 | `ceil(QuantityFactor)` |
| Per Guest | 100000501 | `ceil(GuestCount × QuantityFactor ÷ PackSize)` |
| Per Table | 100000502 | `ceil(TableCount × QuantityFactor)` |

## `stw_planstatus` — Plan Status

| Label | Value |
| --- | --- |
| Draft | 100000600 |
| Shared | 100000601 |
| Converted | 100000602 |
| Abandoned | 100000603 |
