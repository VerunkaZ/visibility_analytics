# Visibility Analytics — Power BI

A Power BI reporting use case built as a source-control-friendly PBIP/TMDL project. The solution transforms separate monthly and weekly media datasets, models them at their native grains, and presents core media and lead-generation KPIs in an interactive dashboard.

> **Project status:** Work in progress — the dashboard and model are implemented, while the final DAX, relationship, and data-quality audit is still pending.

## Project objective

The project was created for a Reporting & Dashboard Specialist use case. Its objectives are to:

- clean and reshape monthly and weekly source data in Power Query;
- preserve the different monthly and weekly grains;
- build a Power BI star schema with shared dimensions;
- create reusable DAX measures for key KPIs;
- present the results through KPI cards, trends, comparisons, slicers, and detail views;
- keep the Power BI solution readable and suitable for Git-based version control.

## Data grain

The model intentionally uses two fact tables rather than combining incompatible time grains.

| Fact table | Grain | Covered periods |
| --- | --- | --- |
| `Monthly` | One metric × category × month, with relevant source attributes | January–July 2026 |
| `Weekly` | One metric × category × week, with relevant source attributes | 13 July–9 August 2026 |

The documented source metrics include:

- Media Cost
- Website Sessions
- Test Drive Leads
- Sales Leads
- Clicks
- Exit to Dealers Website (monthly data only)

## Data model

### Fact tables

- `Monthly`
- `Weekly`

### Shared dimensions

- `Dim Date`
- `Dim Metric`
- `Dim Category`

### Measure table

- `_Measures` — a dedicated container for DAX measures; it is not a fact table.

Monthly and weekly facts remain separate because they have different temporal grains. There is no intended direct relationship between the two fact tables.

## Power Query transformations

The source data was converted from a wide period-column layout into an analytical long format.

### Monthly

- remove non-data rows and blank rows;
- promote headers and assign data types;
- unpivot month columns;
- derive `Month Start Date`;
- rename the resulting label, date, and value fields.

### Weekly

- remove blank rows and normalize column names;
- unpivot week columns;
- derive `Week Start Date` from the source period label;
- preserve a readable `Week Label`;
- remove the temporary `Period flag` after deriving the date.

SQL is not used in the documented solution. Transformations are implemented in Power Query and calculations in DAX.

## DAX measures

The current model contains the following documented measures.

### Monthly

- `Monthly Value`
- `Monthly Media Cost`
- `Monthly Website Sessions`
- `Monthly Test Drive Leads`
- `Monthly TD Leads`
- `Monthly Sales Leads`
- `Monthly TD Media Cost`
- `Monthly Cost of TD Lead`

### Weekly

- `Weekly Value`
- `Weekly Media Cost`
- `Weekly Website Sessions`
- `Weekly Test Drive Leads`
- `Weekly Sales Leads`
- `Weekly TD Media Cost`
- `Weekly Cost of TD Lead`

`Cost of TD Lead` is designed as the ratio of Digital Test Drive media cost to Test Drive Leads, using `DIVIDE` for safe division.

> The exact filter logic and parity of monthly/weekly measure pairs are still under technical review. The measure names above confirm implementation, not final validation.

## Dashboard

The documented dashboard includes:

- KPI cards for media cost, website sessions, test-drive leads, sales leads, and cost per test-drive lead;
- monthly comparison charts;
- trend charts for sessions and leads;
- slicers for period, category, and metric;
- a detailed monthly table.

A Sessions → Test Drive Leads → Sales Leads funnel is not treated as a confirmed business relationship unless its definition is explicitly validated.

## Repository structure

Power BI generates the report and semantic-model folder names. Keep the generated names and references together; do not rename individual artifact folders manually.

```text
visibility_analytics/
├── <Power-BI-generated-name>.Report/
├── <Power-BI-generated-name>.SemanticModel/
├── visibility_analytics.pbip
├── .gitignore
└── README.md
```

The original `.pbix` backup is intentionally stored outside the repository.

## Requirements

- Power BI Desktop with PBIP/PBIR and TMDL support
- Developed and smoke-tested with Power BI Desktop August 2026, version `2.157.1354.0` (x64)
- Access to approved source data and locally configured credentials if a refresh is required

## Open the project

1. Clone or download the repository.
2. Open `visibility_analytics.pbip` in a compatible Power BI Desktop version.
3. Allow Power BI to load the report and semantic model from the generated project folders.
4. Configure approved local data-source credentials if you need to refresh the data.
5. Do not commit credentials, local cache files, or refreshed private data.

Opening the `.pbip` file after fully closing Power BI Desktop serves as the basic smoke test. The test passes when the report, model, tables, relationships, DAX measures, and key visuals load without missing-artifact errors.

## Repository and data policy

The repository is intended to contain source-controlled PBIP/TMDL definitions only.

Excluded from Git:

- `*.pbix` and `*.pbit` files;
- `**/.pbi/cache.abf`;
- `**/.pbi/localSettings.json`;
- credentials and local environment secrets;
- private data exports.

The project should first be published as a private repository. Public visibility requires a separate review of publication rights, metadata, source references, screenshots, and personal or confidential information.

## Validation status and known limitations

Implemented:

- monthly and weekly Power Query transformations;
- separate fact tables for the two grains;
- shared dimensions and star-schema design;
- `_Measures` container and the documented DAX measures;
- dashboard visuals;
- successful PBIP reopen smoke test.

Still to verify before treating the solution as technically final:

- exact DAX expressions and monthly/weekly filter parity;
- relationship cardinality, direction, and active state;
- row-count, null, duplicate, date-mapping, and reconciliation checks;
- dashboard values and interactions after the DAX audit;
- publication rights and absence of sensitive metadata;
- reproducibility after cloning into a clean local folder.

WoW, MoM, and additional trend measures are intentionally deferred until the current measures have been audited.

## Suggested next validation step

Export all measures from `_Measures` in DAX Query View and review them in this order:

1. base aggregations (`Monthly Value`, `Weekly Value`);
2. monthly and weekly measure pairs;
3. filters through `Dim Metric` and `Dim Category`;
4. calculated KPIs such as Cost of TD Lead;
5. only then add WoW, MoM, or further trend measures.

## License

No open-source license is granted at this stage. Publication and reuse rights for the assignment, source material, data, and visual assets must be confirmed before adding a license.
