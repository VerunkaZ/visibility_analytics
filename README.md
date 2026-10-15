# Visibility Analytics — Power BI

## Interactive dashboard

[Open the interactive Power BI dashboard](https://verunkaz.github.io/visibility_analytics/)

Viewing the report requires signing in with an account that has access to it in Power BI.

The project was created for a Reporting & Dashboard Specialist use case. Its objectives are to:

- clean and reshape monthly and weekly source data in Power Query;
- preserve the different monthly and weekly grains;
- build a Power BI star schema with shared dimensions;
- create reusable DAX measures for key KPIs;
- present the results through KPI cards, trends, comparisons, slicers, and detail views;
- keep the Power BI solution readable and suitable for Git-based version control.

## Repository structure

The project is stored in Power BI Project (PBIP) format, with separate report and semantic model definitions.

| File or folder | Purpose |
| --- | --- |
| `visibility_analytics.pbip` | Entry point for opening the project in Power BI Desktop. |
| `visibility_analytics.Report/` | Report definitions, including pages, visuals, layout and theme resources. |
| `visibility_analytics.SemanticModel/` | Data model definitions in TMDL format, including tables, Power Query transformations, relationships and DAX measures. |
| `visibility_analytics.SemanticModel/definition/tables/` | Definitions of `Monthly`, `Weekly`, the shared dimensions and the `_Measures` table. |
| `visibility_analytics.SemanticModel/DAXQueries/` | Saved DAX queries. |
| `docs/index.html` | Web page prepared for displaying the embedded Power BI report through GitHub Pages. |
| `.github/workflows/pbip-ci.yml` | Automated checks for project structure, JSON syntax, file references, excluded files and common credential patterns. |
| `.gitignore` | Excludes Power BI binaries, local cache and settings, and private export folders from version control. |
| `README.md` | Project overview, scope, data notes and repository guide. |

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


## Dashboard structure

- **Monthly Overview** — January to July 2026; a monthly view of reach, traffic, leads, media spend and cost-efficiency trends.
- **Weekly Overview** — 13 July to 9 August 2026; a weekly view of the same core metrics for a more detailed look at recent performance.
- **Data Quality & Definitions** — metric definitions, calculation logic and source-data limitations needed to interpret the report correctly.
  - KPI cards for media cost, website sessions, test-drive leads, sales leads, and cost per test-drive lead;
  - comparison charts;
  - trend charts for sessions and leads;
  - slicers for period, category, and metric;

## Note

The source data uses two different definitions for the row labelled **Cost of TD Lead from TD Campaigns**. In Monthly, cost is divided by Page Views; in Weekly, it is divided by Test Drive Leads. For the report consistency is used only one definition: 

**Digital cost per lead = Digital Test Drive media cost / Digital Test Drive leads**

For example, the report shows approximately 11.6 for June and 67.9 for July, while the Monthly source shows approximately 0.658 and 4.345 because it uses Page Views as the denominator. The source workbook itself was not changed.

## Data notes

The source does not define whether Digital All, Digital Meta and Digital Test Drive overlap, or how repeated lead records are handled. Totals should therefore be read as sums of recorded values rather than confirmed deduplicated spend or unique prospects.

Most cost rows are marked as EUR, but the currency for Digital Meta is not specified. Sessions, test drive leads and sales leads are also not confirmed as successive stages of the same customer journey.

## **Scope note:**

This README documents the submitted dashboard and the original assignment. It does not extend the project scope or propose additional dashboard features.

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

## Power Query transformations for the source data

- remove non-data rows and blank rows;
- promote headers and assign data types;
- unpivot columns;
- normalize column names;
- derive `Month Start Date &`Week Start Date` from the source period label;#;
- rename the resulting label, date, and value fields.
- preserve a readable `Week Label`;
- remove the temporary `Period flag` after deriving the date.

SQL is not used in the documented solution. Transformations are implemented in Power Query and calculations in DAX.

## DAX measures note

`Cost of TD Lead` is designed as the ratio of Digital Test Drive media cost to Test Drive Leads, using `DIVIDE` for safe division.



## License

No open-source license is granted at this stage. Publication and reuse rights for the assignment, source material, data, and visual assets must be confirmed before adding a license.
