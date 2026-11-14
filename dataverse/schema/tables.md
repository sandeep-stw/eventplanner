# Dataverse schema — tables & columns

Publisher prefix: **`stw`**. Existing solution: **`EventPlanner`**.

Five tables. Three are reference data you load once (Theme, Product, Theme Product);
two capture the output of a conversation (Event Plan, Event Plan Line).

---

## 1. Theme — `stw_theme`

The fixed, curated looks the agent sells. Display name **Theme**, plural **Themes**,
primary name column **`stw_name`** ("Theme Name").

| Display name | Logical name | Type | Notes |
| --- | --- | --- | --- |
| Theme Name | `stw_name` | Text (100) | Primary column. e.g. "Tropical Luau" |
| Theme Code | `stw_themecode` | Text (10) | Unique key the agent passes between flows. e.g. `TROP` |
| Tagline | `stw_tagline` | Text (160) | One-line sales hook shown on the theme card |
| Description | `stw_description` | Multiline text (2000) | 2–3 sentences for the detail view |
| Occasion | `stw_occasion` | Choice (`stw_occasion`) | Multi-select **off**; see [choices.md](./choices.md) |
| Vibe | `stw_vibe` | Choice (`stw_vibe`) | Used by the discovery question |
| Color Palette | `stw_colorpalette` | Text (200) | Comma separated, e.g. "Coral, Palm Green, Sand" |
| Image URL | `stw_imageurl` | URL (400) | Hero image for the adaptive card |
| Difficulty | `stw_difficulty` | Choice (`stw_difficulty`) | Easy / Moderate / Ambitious |
| Est. Setup Hours | `stw_setuphours` | Decimal (1 dp) | Total DIY assembly time for the core kit |
| Base Cost Per Guest | `stw_basecostperguest` | Currency | Ballpark used for budget pre-screening |
| Min Guests | `stw_minguests` | Whole number | Hide theme if guest count below this |
| Max Guests | `stw_maxguests` | Whole number | Hide theme if guest count above this |
| Popularity Rank | `stw_popularityrank` | Whole number | 1 = best seller; drives sort order |
| Is Active | `stw_isactive` | Yes/No | Default **Yes**. Flows filter on this |

**Alternate key:** `stw_themecode_key` on `stw_themecode` — lets flows retrieve a theme
without a GUID.

---

## 2. Product — `stw_product`

The store catalog. Display name **Product**, plural **Products**, primary name column
**`stw_name`**.

| Display name | Logical name | Type | Notes |
| --- | --- | --- | --- |
| Product Name | `stw_name` | Text (150) | Primary column |
| SKU | `stw_sku` | Text (20) | Alternate key |
| Category | `stw_category` | Choice (`stw_category`) | Balloons, Backdrops, Tableware, … |
| Description | `stw_description` | Multiline text (1000) | Shown on request ("tell me more about…") |
| Unit Price | `stw_unitprice` | Currency | |
| Pack Size | `stw_packsize` | Whole number | Units per SKU, e.g. 50 plates |
| Unit Of Measure | `stw_unitofmeasure` | Text (30) | "pack", "each", "set", "roll" |
| Stock Quantity | `stw_stockquantity` | Whole number | Flows exclude anything `<= 0` |
| Image URL | `stw_imageurl` | URL (400) | |
| DIY Difficulty | `stw_diydifficulty` | Choice (`stw_difficulty`) | |
| Assembly Minutes | `stw_assemblyminutes` | Whole number | Rolled up into "plan setup time" |
| Is Active | `stw_isactive` | Yes/No | Default **Yes** |

**Alternate key:** `stw_sku_key` on `stw_sku`.

---

## 3. Theme Product — `stw_themeproduct`

The junction table that makes a theme *fixed*: it is the only source of items the agent
is allowed to recommend for a theme. Primary name column `stw_name` is populated as
`{ThemeCode}-{SKU}` on import.

| Display name | Logical name | Type | Notes |
| --- | --- | --- | --- |
| Name | `stw_name` | Text (100) | Primary column, e.g. `TROP-BAL-001` |
| Theme | `stw_themeid` | Lookup → `stw_theme` | Required |
| Product | `stw_productid` | Lookup → `stw_product` | Required |
| Role | `stw_role` | Choice (`stw_role`) | **Core** / **Accent** / **Upgrade** — drives budget trimming |
| Quantity Basis | `stw_quantitybasis` | Choice (`stw_quantitybasis`) | PerEvent / PerGuest / PerTable |
| Quantity Factor | `stw_quantityfactor` | Decimal (2 dp) | Multiplied by the basis, then rounded **up** |
| Display Order | `stw_displayorder` | Whole number | Order within the plan |
| Styling Tip | `stw_stylingtip` | Text (300) | One-line how-to/upsell line the agent can read out |

Relationships: 1:N from Theme and from Product, both **Referential, Restrict Delete**
so reference data can't be orphaned.

---

## 4. Event Plan — `stw_eventplan`

One row per plan the agent produces. Primary name column `stw_name`.

| Display name | Logical name | Type | Notes |
| --- | --- | --- | --- |
| Plan Name | `stw_name` | Text (150) | e.g. "Priya's Tropical Luau — 12 Jun" |
| Plan Number | `stw_plannumber` | Autonumber `PLAN-{SEQNUM:00000}` | Shared with the shopper |
| Theme | `stw_themeid` | Lookup → `stw_theme` | |
| Customer Name | `stw_customername` | Text (100) | |
| Customer Email | `stw_customeremail` | Email (150) | |
| Event Date | `stw_eventdate` | Date only | |
| Guest Count | `stw_guestcount` | Whole number | |
| Table Count | `stw_tablecount` | Whole number | Derived at 8 guests/table when not asked |
| Budget | `stw_budget` | Currency | What the shopper said they'd spend |
| Estimated Total | `stw_estimatedtotal` | Currency | Sum of included line totals (set by flow) |
| Estimated Setup Hours | `stw_estimatedsetuphours` | Decimal (1 dp) | |
| Status | `stw_status` | Choice (`stw_planstatus`) | Draft / Shared / Converted / Abandoned |
| Conversation Id | `stw_conversationid` | Text (100) | `System.Conversation.Id`, for analytics joins |

---

## 5. Event Plan Line — `stw_eventplanline`

| Display name | Logical name | Type | Notes |
| --- | --- | --- | --- |
| Name | `stw_name` | Text (150) | Product name snapshot |
| Event Plan | `stw_eventplanid` | Lookup → `stw_eventplan` | **Parental** cascade so lines delete with the plan |
| Product | `stw_productid` | Lookup → `stw_product` | |
| Role | `stw_role` | Choice (`stw_role`) | Copied from the mapping |
| Quantity | `stw_quantity` | Whole number | |
| Unit Price | `stw_unitprice` | Currency | Price snapshot at plan time |
| Line Total | `stw_linetotal` | Currency | `Quantity × Unit Price` |
| Included | `stw_included` | Yes/No | `No` when trimmed to fit budget — kept so the agent can upsell later |

---

## Views worth creating

These make manual QA much faster:

- **Theme → Active Themes by Popularity**: `stw_isactive = Yes`, sorted by `stw_popularityrank` asc.
- **Product → In Stock Catalog**: `stw_isactive = Yes AND stw_stockquantity > 0`, grouped by category.
- **Theme Product → Mapping by Theme**: Theme, Product, Role, Quantity Basis, Quantity Factor, Display Order.
- **Event Plan → Plans This Week**: `createdon` last 7 days, with Estimated Total and Status.
