# Master Development Prompt — Open-Source Configurable BI Platform

## 1. ROLE

You are a principal software architect, senior full-stack engineer, data-platform architect, visualization engineer, UX designer, security engineer, DevOps engineer, QA engineer, and open-source maintainer.

Your task is to design and implement a production-grade, open-source Business Intelligence and reporting platform similar in capability to Tableau, Power BI, Metabase, and Apache Superset.

The platform must be:

- Open source
- Self-hostable
- Vendor neutral
- Database agnostic
- Highly configurable
- Extensible through plugins
- API-first
- Secure
- Scalable
- Usable by non-technical users
- Powerful enough for technical users
- Capable of generating interactive reports and dashboards
- Capable of exporting reports and datasets to Excel
- Capable of presenting data through charts and graphical visualizations
- Suitable for embedding into other applications
- Designed to support future AI/LLM/agent integration

Do not build a toy application or a demo.

Build a proper software platform with clean architecture, modularity, documentation, automated tests, observability, security, and deployment support.

---

# 2. PRODUCT VISION

Create a general-purpose BI platform where an organization can connect its data sources, define reusable datasets/data models, create reports and dashboards using a visual interface, configure visualizations, apply filters and calculations, schedule reports, export results, and share or embed dashboards.

A user should be able to go from:

```text
Data Source
    ↓
Connection
    ↓
Dataset / Semantic Model
    ↓
Query / Data Model
    ↓
Visualization
    ↓
Report
    ↓
Dashboard
    ↓
Share / Export / Schedule / Embed
```

The entire process should be configurable.

Avoid hard-coding business logic wherever possible.

---

# 3. CORE DESIGN PRINCIPLE

The most important architectural principle is:

## "Configuration over hard-coded behavior."

Reports, dashboards, charts, filters, data models, calculations, layouts, permissions, themes, exports, schedules, and connectors should be represented using configuration and metadata.

For example:

```json
{
  "report": {
    "name": "Sales Performance",
    "version": 1,
    "dataset": "sales",
    "filters": [],
    "components": [],
    "layout": {},
    "theme": {},
    "permissions": {}
  }
}
```

The rendering engine should interpret this configuration.

Do not create a separate hard-coded implementation for every report.

---

# 4. TARGET USERS

Support multiple types of users.

## Business User

Should be able to:

- Select a dataset
- Select dimensions
- Select measures
- Drag and drop fields
- Apply filters
- Create charts
- Create tables
- Create KPIs
- Build dashboards
- Export to Excel
- Save reports
- Share reports

No SQL should be required.

## Analyst

Should additionally be able to:

- Write SQL
- Create calculated fields
- Create calculated measures
- Create joins
- Create reusable datasets
- Define parameters
- Create advanced visualizations
- Create custom queries

## Administrator

Should be able to:

- Manage users
- Manage groups
- Manage permissions
- Manage data sources
- Manage datasets
- Manage connectors
- Manage system configuration
- Manage themes
- Manage audit logs
- Manage schedules
- Manage plugins

## Developer

Should be able to:

- Create plugins
- Add connectors
- Add visualization types
- Add data processors
- Add authentication providers
- Add export formats
- Add APIs
- Extend the platform without modifying core code

---

# 5. FUNCTIONAL REQUIREMENTS

## 5.1 Authentication

Implement:

- Username/password authentication
- Secure password hashing
- Session/token management
- Logout
- Password reset architecture
- Email verification architecture
- Role-based access control
- Group-based permissions

Design the authentication subsystem so that future providers can be added:

- OAuth2
- OpenID Connect
- Google
- Microsoft
- LDAP
- SAML

Do not tightly couple authentication to the application.

---

# 6. USER AND ORGANIZATION MANAGEMENT

Support:

- Users
- Groups
- Roles
- Organizations/workspaces
- Projects/folders
- Ownership
- Sharing

Example:

```text
Organization
 ├── Users
 ├── Groups
 ├── Data Sources
 ├── Datasets
 ├── Reports
 ├── Dashboards
 └── Schedules
```

Design the model so the platform can operate as:

1. Single-user installation
2. Team installation
3. Multi-user organization
4. Multi-tenant SaaS deployment

Multi-tenancy should not be mandatory for a simple self-hosted deployment.

---

# 7. DATA SOURCE ARCHITECTURE

Create a pluggable connector architecture.

The platform should not assume one database.

Initial connectors should include, where practical:

### Relational

- PostgreSQL
- MySQL
- MariaDB
- SQLite
- Microsoft SQL Server

### Analytical

- DuckDB
- ClickHouse

### Files

- CSV
- Excel
- Parquet
- JSON

Design the connector interface so future connectors can be added without changing the query engine.

Potential future connectors:

- Oracle
- Snowflake
- BigQuery
- Redshift
- Databricks
- Trino
- Presto
- REST APIs
- GraphQL APIs
- Google Sheets

---

# 8. DATA SOURCE CONNECTION MANAGEMENT

Create a Data Sources section.

Users should be able to:

- Add data source
- Edit connection
- Test connection
- Enable/disable connection
- View connection health
- Delete connection
- Configure credentials
- Configure SSL
- Configure connection pooling
- Configure timeout
- Configure metadata refresh

Credentials must never be exposed to normal users.

Secrets must be encrypted at rest.

Never log passwords, tokens, connection strings containing credentials, or secrets.

---

# 9. DATASET / SEMANTIC MODEL

Create a reusable dataset/semantic-model layer.

A dataset can represent:

- A table
- A database view
- A SQL query
- Multiple joined tables
- A logical model
- A file
- A transformed dataset

Dataset metadata should include:

```text
Dataset
 ├── Fields
 │    ├── Name
 │    ├── Data Type
 │    ├── Display Name
 │    ├── Description
 │    ├── Hidden
 │    ├── Format
 │    └── Role
 │
 ├── Dimensions
 ├── Measures
 ├── Relationships
 ├── Calculated Fields
 ├── Filters
 ├── Parameters
 └── Permissions
```

---

# 10. DATA TYPES

Support at minimum:

- String
- Integer
- Decimal
- Float
- Boolean
- Date
- DateTime
- Time
- JSON
- Binary where applicable

Allow semantic formatting:

```text
Currency
Percentage
Number
Date
DateTime
Duration
Custom
```

Example:

```text
revenue
data_type: decimal
semantic_type: currency
currency: INR
decimal_places: 2
```

---

# 11. DIMENSIONS AND MEASURES

Clearly distinguish:

### Dimensions

Examples:

- Country
- State
- City
- Product
- Department
- Customer
- Date

### Measures

Examples:

- Revenue
- Quantity
- Profit
- Cost
- Average Order Value

Support aggregations:

- SUM
- AVG
- COUNT
- COUNT DISTINCT
- MIN
- MAX
- MEDIAN where supported
- Standard deviation where supported

Design aggregation handling so database-specific capabilities can be supported.

---

# 12. CALCULATED FIELDS

Allow users to create formulas.

Examples:

```text
profit = revenue - cost
margin = profit / revenue
growth = current_period / previous_period - 1
```

Create a calculation engine/AST rather than directly concatenating user input into SQL.

The calculation system must prevent SQL injection.

Support functions such as:

```text
SUM()
AVG()
COUNT()
MIN()
MAX()

IF()
CASE()
COALESCE()

ROUND()
ABS()

YEAR()
MONTH()
DAY()
DATE_TRUNC()

CONCAT()
LOWER()
UPPER()

NULLIF()
```

Create an extensible function registry.

---

# 13. QUERY ENGINE

Create a query abstraction layer.

The frontend should not construct raw SQL.

Use an intermediate query representation.

Example:

```json
{
  "dataset": "sales",
  "dimensions": ["country", "month"],
  "measures": [
    {
      "field": "revenue",
      "aggregation": "SUM"
    }
  ],
  "filters": [],
  "sort": [],
  "limit": 1000
}
```

The backend converts this representation into database-specific queries.

Architecture:

```text
Visualization
      ↓
Query Specification
      ↓
Query Planner
      ↓
Semantic Layer
      ↓
SQL / Query Generator
      ↓
Database Connector
      ↓
Result Processor
      ↓
Visualization
```

---

# 14. QUERY SAFETY

Never trust frontend-generated SQL.

Implement:

- Query validation
- Parameterized queries
- SQL injection protection
- Dataset permission validation
- Column permission validation
- Row-level security architecture
- Query timeout
- Result size limits
- Pagination
- Rate limiting

---

# 15. QUERY CACHE

Create a configurable query cache.

Cache key should consider:

- Dataset
- Query
- Filters
- Parameters
- User security context where necessary

Support:

- Enable/disable cache
- TTL
- Manual cache invalidation
- Automatic invalidation after dataset changes

Design cache abstraction so Redis can be used later.

---

# 16. REPORT BUILDER

Create a visual report builder.

The user should be able to:

1. Create report
2. Select dataset
3. Add visualization
4. Select dimensions
5. Select measures
6. Apply filters
7. Configure chart
8. Arrange components
9. Save
10. Preview
11. Publish

The builder should support drag-and-drop.

---

# 17. VISUALIZATION ENGINE

Create a plugin-based visualization architecture.

Initial visualizations:

### Basic

- Table
- Pivot table
- KPI
- Text
- Number
- Progress indicator

### Charts

- Bar chart
- Horizontal bar
- Line chart
- Area chart
- Pie chart
- Donut chart
- Scatter plot
- Bubble chart
- Histogram

### Advanced

- Heatmap
- Treemap
- Funnel
- Gauge
- Combo chart
- Waterfall
- Box plot

### Geographic

- Map
- Choropleth
- Point map

Use an open-source visualization library with a permissive license.

Do not depend on proprietary visualization libraries.

Create a visualization interface such as:

```typescript
interface VisualizationPlugin {
    type: string;
    name: string;
    category: string;

    getConfigSchema(): Schema;

    validateConfig(config: unknown): ValidationResult;

    buildQuery(config: VisualizationConfig): QuerySpec;

    render(
        container: HTMLElement,
        data: VisualizationData,
        config: VisualizationConfig
    ): void;
}
```

---

# 18. CHART CONFIGURATION

Every visualization should have configurable properties.

Example:

```text
Chart
 ├── Data
 ├── X Axis
 ├── Y Axis
 ├── Group By
 ├── Sort
 ├── Filters
 ├── Colors
 ├── Labels
 ├── Legend
 ├── Tooltip
 ├── Grid
 ├── Axis
 ├── Title
 ├── Formatting
 └── Interactions
```

Do not hard-code chart styling.

---

# 19. DASHBOARD BUILDER

Reports should be composable into dashboards.

Dashboard features:

- Multiple widgets
- Drag and drop
- Resize
- Grid layout
- Responsive layout
- Dashboard-level filters
- Widget-level filters
- Cross-filtering
- Drill-down
- Drill-through
- Linked charts
- KPI cards
- Text blocks
- Images/logos
- Tabs/pages

Example:

```text
Dashboard
 ├── Header
 ├── Global Filters
 ├── KPI Row
 ├── Charts
 ├── Tables
 └── Detail Section
```

---

# 20. FILTER SYSTEM

Create a reusable filter framework.

Filter types:

- Text
- Number
- Date
- Date range
- Multi-select
- Single-select
- Search
- Boolean
- Relative date

Examples:

```text
Date = Last 30 Days

Country IN ["India", "USA"]

Revenue > 100000

Product contains "Laptop"
```

Filters must be represented as metadata.

Support filter scope:

- Visualization
- Report
- Dashboard
- Dataset

---

# 21. PARAMETERS

Support user-configurable parameters.

Example:

```text
Parameter:
Name: Target Revenue
Type: Number
Default: 1000000
```

Users should be able to use parameters in:

- Calculations
- Filters
- Queries
- Visualization configuration

---

# 22. INTERACTIVE DASHBOARDS

Support:

- Hover interactions
- Click interactions
- Cross filtering
- Highlighting
- Drill down
- Drill through
- Navigation
- Filter propagation

Example:

User clicks:

```text
India
```

Other charts automatically filter to:

```text
Country = India
```

---

# 23. TABLE AND PIVOT TABLE

Create a powerful table component.

Features:

- Sorting
- Filtering
- Pagination
- Column resize
- Column reorder
- Hide/show columns
- Freeze columns
- Formatting
- Conditional formatting
- Totals
- Subtotals
- Grouping
- Search
- Export

Pivot table:

```text
Rows
Columns
Values
Filters
```

Support configurable aggregation.

---

# 24. CONDITIONAL FORMATTING

Allow rules such as:

```text
IF profit < 0 → negative formatting

IF margin > 30% → highlight

IF revenue > target → highlight
```

Rules must be configuration driven.

---

# 25. EXCEL EXPORT

Excel export is a first-class feature.

Users should be able to export:

- Current visualization
- Table
- Pivot table
- Entire report
- Dashboard data
- Raw dataset

Excel output should support:

- Multiple worksheets
- Headers
- Formatting
- Number formats
- Dates
- Currency
- Filters
- Freeze panes
- Auto widths where practical
- Totals
- Metadata sheet

Example:

```text
Sales Report.xlsx

Sheet 1: Summary
Sheet 2: Sales by Region
Sheet 3: Sales by Product
Sheet 4: Raw Data
```

Do not expose raw database credentials or internal metadata in exports.

---

# 26. OTHER EXPORT FORMATS

Design an export framework.

Initial formats:

- XLSX
- CSV
- JSON
- PDF where practical

Future:

- PNG
- SVG
- PowerPoint

Create:

```text
ExportService
 ├── ExcelExporter
 ├── CsvExporter
 ├── JsonExporter
 └── PdfExporter
```

---

# 27. REPORT TEMPLATES

Allow reusable report templates.

Examples:

```text
Sales Dashboard
Financial Dashboard
HR Dashboard
Inventory Dashboard
Healthcare Dashboard
Operations Dashboard
```

Templates should contain configuration, not application-specific code.

Allow:

- Save as template
- Clone template
- Import template
- Export template
- Version template

---

# 28. REPORT VERSIONING

Implement report versioning.

Every saved report should have:

```text
Report
 ├── Version 1
 ├── Version 2
 ├── Version 3
 └── Published Version
```

Support:

- Draft
- Published
- Archived

Allow rollback.

---

# 29. REPORT IMPORT / EXPORT

Reports should be portable.

Provide a JSON-based report definition format.

Example:

```json
{
  "schemaVersion": "1.0",
  "report": {},
  "datasets": [],
  "visualizations": [],
  "layout": {},
  "theme": {}
}
```

A report should be exportable and importable into another installation.

Never serialize secrets into report files.

---

# 30. THEMING

Create a theme system.

Everything visual should be configurable.

Theme configuration should support:

- Fonts
- Font sizes
- Background
- Borders
- Chart defaults
- Spacing
- Radius
- Shadows
- Table style
- KPI style
- Dashboard style

Support:

- Light theme
- Dark theme
- Custom theme

---

# 31. UI CONFIGURATION

Avoid hard-coded menus and navigation.

Where appropriate, provide configuration for:

- Navigation
- Feature visibility
- Branding
- Logo
- Organization name
- Default theme
- Default landing page
- Enabled modules

---

# 32. INTERNATIONALIZATION

Design for i18n from the beginning.

Support:

- English initially
- Translation files
- Date formats
- Number formats
- Currency formats
- Time zones
- RTL architecture

Do not hard-code user-facing strings.

---

# 33. ACCESS CONTROL

Implement RBAC.

Example roles:

```text
Super Admin
Organization Admin
Data Admin
Analyst
Report Creator
Viewer
```

Permissions should be granular.

Example:

```text
dataset.read
dataset.create
dataset.update
dataset.delete

report.read
report.create
report.update
report.delete
report.publish

dashboard.read
dashboard.create

datasource.manage
user.manage
```

---

# 34. ROW-LEVEL SECURITY

Design support for:

```text
User A → India only

User B → USA only

Admin → All countries
```

Row-level policies should be evaluated server-side.

Never rely on frontend filtering for security.

---

# 35. COLUMN-LEVEL SECURITY

Support restricted fields.

Example:

```text
Normal user:
salary → hidden

HR user:
salary → visible
```

Security enforcement must happen before query results reach the frontend.

---

# 36. AUDIT LOGGING

Create an audit subsystem.

Record events such as:

- Login
- Logout
- Dataset creation
- Dataset modification
- Report creation
- Report modification
- Report publication
- Report deletion
- Permission changes
- Data-source changes
- Export
- Administrative operations

Audit records should include:

```text
timestamp
user
action
resource
resource_id
IP where appropriate
result
metadata
```

Do not log sensitive secrets.

---

# 37. SCHEDULING

Design a report scheduling subsystem.

Users should eventually be able to configure:

```text
Every day at 8:00 AM

Every Monday

First day of every month
```

Actions:

- Generate report
- Export Excel
- Export PDF
- Send email
- Store generated artifact

Create a scheduler abstraction rather than coupling scheduling directly to reports.

---

# 38. NOTIFICATIONS

Design notification abstraction.

Future channels:

- Email
- Webhook
- Slack
- Microsoft Teams

Do not make external services mandatory.

---

# 39. EMBEDDING

Reports and dashboards should be embeddable.

Support architecture for:

```html
<iframe>
```

and preferably a JavaScript SDK:

```javascript
BI.embedDashboard({
    dashboardId: "...",
    token: "..."
});
```

Support secure signed embedding tokens.

Never expose administrative credentials to embedded clients.

---

# 40. REST API

Create a documented REST API.

Major resources:

```text
/auth
/users
/groups
/organizations
/data-sources
/datasets
/fields
/queries
/reports
/dashboards
/visualizations
/filters
/templates
/exports
/schedules
/audit
```

Use OpenAPI.

The frontend must consume the API rather than accessing the database directly.

---

# 41. FUTURE GRAPHQL SUPPORT

Keep API architecture sufficiently modular that GraphQL can be added later.

Do not implement GraphQL unless it is genuinely useful during the initial development phase.

---

# 42. WEBHOOKS

Create a webhook framework.

Events:

```text
report.created
report.updated
report.published
dashboard.updated
dataset.updated
export.completed
schedule.completed
```

---

# 43. PLUGIN ARCHITECTURE

This is a major requirement.

Create a plugin SDK.

Plugins should eventually support:

```text
Data Connector
Visualization
Exporter
Authentication Provider
Data Transformer
Calculation Function
Notification Provider
Theme
```

Example:

```text
plugins/
    postgres/
    mysql/
    csv/
    echarts/
    excel/
```

Plugins should declare metadata:

```json
{
  "name": "example-plugin",
  "version": "1.0.0",
  "type": "visualization",
  "apiVersion": "1"
}
```

Version the plugin API.

Avoid breaking plugin compatibility unnecessarily.

---

# 44. DATA TRANSFORMATION

Design a transformation pipeline.

Example:

```text
Source
 ↓
Filter
 ↓
Rename
 ↓
Calculate
 ↓
Join
 ↓
Aggregate
 ↓
Dataset
```

Initial transformations may be SQL-based.

Design interfaces for future:

- Python transformations
- DuckDB transformations
- dbt integration
- ETL pipelines

Do not introduce arbitrary Python execution inside the server without a strong sandbox/security design.

---

# 45. MATERIALIZED DATASETS

Design optional support for materialized datasets.

Useful for expensive queries.

Architecture:

```text
Source
 ↓
Transformation
 ↓
Materialization
 ↓
Analytics Query
```

Allow configurable refresh:

- Manual
- Scheduled
- On demand

---

# 46. PERFORMANCE

Design for large datasets.

Do not load millions of rows into the browser.

Use:

- Server-side aggregation
- Pagination
- Query limits
- Streaming where appropriate
- Caching
- Lazy loading
- Background jobs
- Incremental loading
- Database pushdown

The frontend should normally receive aggregated results rather than raw large datasets.

---

# 47. ASYNCHRONOUS QUERY EXECUTION

For expensive queries:

```text
Frontend
 ↓
Create Query Job
 ↓
Job Queue
 ↓
Worker
 ↓
Database
 ↓
Result Storage
 ↓
Frontend polling/websocket
```

Support:

- Query status
- Progress where available
- Cancellation
- Timeout
- Error handling

---

# 48. BACKGROUND JOB SYSTEM

Create an abstraction for background jobs.

Possible jobs:

```text
Query execution
Excel generation
PDF generation
Dataset refresh
Cache refresh
Scheduled reports
Notifications
```

Use a queue architecture that can start simple and scale later.

---

# 49. OBSERVABILITY

Implement:

- Structured logging
- Metrics
- Health checks
- Readiness checks
- Error tracking architecture
- Query execution timing
- API latency
- Job status
- Database connection status

Provide:

```text
/health
/ready
/metrics
```

where appropriate.

---

# 50. ERROR HANDLING

Errors must be understandable.

Bad:

```text
Internal Server Error
```

Better:

```text
Unable to execute the query.

Reason:
Column "revenue_total" does not exist.

Dataset:
Sales

Suggested action:
Refresh dataset metadata or verify the field configuration.
```

Never expose stack traces to normal users.

---

# 51. FRONTEND UX

The UI should feel like a professional BI product.

Main navigation:

```text
Home
Dashboards
Reports
Datasets
Data Sources
Templates
Schedules
Administration
```

Possible workspace:

```text
┌─────────────────────────────────────────────┐
│ Toolbar                                     │
├──────────────┬──────────────────────────────┤
│ Data Fields  │                              │
│              │       Report Canvas          │
│ Dimensions   │                              │
│ Measures     │                              │
│              │                              │
├──────────────┴──────────────────────────────┤
│ Properties / Configuration                  │
└─────────────────────────────────────────────┘
```

---

# 52. REPORT DESIGN EXPERIENCE

The report builder should support:

- Drag field onto canvas
- Drag field onto chart
- Automatically suggest visualization
- Change chart type
- Configure properties
- Duplicate visualization
- Delete visualization
- Resize
- Align
- Group
- Copy/paste
- Undo/redo

Make common tasks discoverable.

---

# 53. AUTO VISUALIZATION

Design an optional recommendation engine.

Example:

If the user selects:

```text
Date
Revenue
```

Suggest:

```text
Line chart
```

If:

```text
Country
Revenue
```

Suggest:

```text
Bar chart
```

If:

```text
Category
Revenue
```

Suggest:

```text
Bar / Pie
```

This should be rule/configuration driven initially.

Keep the architecture ready for an AI recommendation engine later.

---

# 54. DATA PREVIEW

Dataset designer must show:

- Sample rows
- Columns
- Data types
- Null counts
- Distinct values
- Basic statistics

Do not scan huge datasets unnecessarily.

Use sampling.

---

# 55. DATA PROFILING

Provide optional profiling:

```text
Row count
Null percentage
Distinct count
Min
Max
Average
Top values
```

Make profiling configurable because it may be expensive.

---

# 56. SEARCH

Provide global search across:

- Reports
- Dashboards
- Datasets
- Data sources
- Templates

Later architecture should allow semantic search.

---

# 57. FAVORITES / RECENT ITEMS

Users should have:

- Favorites
- Recently opened
- Recently created
- Recently modified

---

# 58. SHARING

Support:

- Private
- Organization
- Group
- Specific users
- Public link where administrator allows

Public links must use secure tokens and configurable expiration.

---

# 59. VERSIONED CONFIGURATION

All major platform objects should have versionable metadata.

Examples:

```text
Dataset definition
Report definition
Dashboard definition
Visualization configuration
Theme
Template
```

Use schema versions.

Example:

```json
{
  "schemaVersion": "1.2"
}
```

Provide migration mechanisms when schemas change.

---

# 60. DATABASE ARCHITECTURE

Use a metadata database for platform configuration.

Recommended initial choice:

PostgreSQL.

Store:

- Users
- Organizations
- Permissions
- Data sources
- Dataset definitions
- Reports
- Dashboards
- Visualization configurations
- Themes
- Schedules
- Audit logs
- Jobs

Do NOT store analytical business data in the platform metadata database unless explicitly required.

---

# 61. RECOMMENDED TECHNOLOGY STACK

Prefer a modern, open-source stack.

A strong default architecture is:

## Frontend

- React
- TypeScript
- Vite
- Modern component library
- React Query/TanStack Query
- Zustand or equivalent lightweight state management
- React Hook Form
- JSON Schema based configuration forms

## Backend

Prefer:

- Python
- FastAPI
- Pydantic
- SQLAlchemy
- Alembic

The backend must expose a clean API boundary.

## Metadata Database

- PostgreSQL

## Analytical Engine

Use database pushdown wherever possible.

For file-based analytics and local analytical processing, consider:

- DuckDB

## Visualization

Prefer:

- Apache ECharts

or another permissively licensed mature visualization library.

## Excel

Use an open-source Excel generation library.

## Containers

- Docker
- Docker Compose

## API Documentation

- OpenAPI

## Testing

Frontend:

- Vitest
- Playwright

Backend:

- Pytest

## Code Quality

Use:

- ESLint
- Prettier
- Ruff
- MyPy where appropriate

The exact stack may be adjusted if there is a compelling architectural reason.

Do not introduce unnecessary technologies.

---

# 62. REPOSITORY STRUCTURE

Use a maintainable monorepo or clearly separated repositories.

Preferred structure:

```text
bi-platform/
│
├── frontend/
│   ├── src/
│   ├── components/
│   ├── features/
│   ├── pages/
│   ├── services/
│   ├── plugins/
│   └── tests/
│
├── backend/
│   ├── app/
│   │   ├── api/
│   │   ├── auth/
│   │   ├── users/
│   │   ├── organizations/
│   │   ├── datasources/
│   │   ├── datasets/
│   │   ├── query/
│   │   ├── reports/
│   │   ├── dashboards/
│   │   ├── visualizations/
│   │   ├── exports/
│   │   ├── scheduler/
│   │   ├── jobs/
│   │   ├── plugins/
│   │   ├── audit/
│   │   └── core/
│   └── tests/
│
├── shared/
│   ├── schemas/
│   └── types/
│
├── plugins/
│
├── docs/
│
├── deployment/
│
├── examples/
│
├── scripts/
│
├── docker-compose.yml
├── README.md
└── LICENSE
```

Adapt the structure if a better architecture emerges, but maintain clear module boundaries.

---

# 63. DOMAIN MODEL

Design explicit domain entities.

At minimum:

```text
User
Organization
Group
Role
Permission

DataSource
DataSourceCredential

Dataset
DatasetField
DatasetRelationship
CalculatedField
Measure
Dimension

Query
QueryJob

Visualization
VisualizationConfig

Report
ReportVersion

Dashboard
DashboardVersion

Filter
Parameter

Theme
Template

Schedule
ExportJob

AuditEvent
Plugin
```

Use proper foreign keys, indexes, constraints, and lifecycle states.

---

# 64. API DESIGN

Use RESTful APIs.

Example:

```text
GET    /api/v1/datasets
POST   /api/v1/datasets
GET    /api/v1/datasets/{id}
PUT    /api/v1/datasets/{id}
DELETE /api/v1/datasets/{id}

POST   /api/v1/queries/validate
POST   /api/v1/queries/execute

GET    /api/v1/reports
POST   /api/v1/reports

GET    /api/v1/dashboards
POST   /api/v1/dashboards

POST   /api/v1/exports/excel
GET    /api/v1/jobs/{id}
```

Use API versioning.

---

# 65. SECURITY PRINCIPLES

Treat security as a first-class feature.

Implement:

- Password hashing
- Secure sessions/tokens
- RBAC
- Dataset permissions
- Row-level security architecture
- Column-level security
- Input validation
- Parameterized SQL
- CSRF protection where applicable
- CORS configuration
- Rate limiting
- Secure headers
- Secret encryption
- Audit logging
- Secure file handling
- Upload validation
- SSRF protection for URL-based connectors
- Resource limits

Do not execute arbitrary user-provided code.

---

# 66. FILE UPLOAD SECURITY

For CSV/XLSX/JSON/Parquet uploads:

- Validate file type
- Validate file size
- Store files outside executable paths
- Generate safe filenames
- Scan/validate content
- Prevent path traversal
- Prevent formula injection in Excel exports where applicable
- Apply quotas

---

# 67. OPEN-SOURCE LICENSING

Choose a genuinely open-source license.

Prefer:

```text
Apache License 2.0
```

unless there is a strong reason to use another OSI-approved license.

Review every third-party dependency for license compatibility.

Do not introduce proprietary dependencies into the core platform.

Maintain a third-party license inventory.

---

# 68. DOCUMENTATION

Create excellent documentation.

At minimum:

```text
README.md

docs/
 ├── architecture.md
 ├── installation.md
 ├── configuration.md
 ├── data-sources.md
 ├── datasets.md
 ├── reports.md
 ├── dashboards.md
 ├── visualizations.md
 ├── exports.md
 ├── security.md
 ├── plugins.md
 ├── api.md
 ├── development.md
 ├── deployment.md
 └── troubleshooting.md
```

Provide examples.

---

# 69. DOCKER DEPLOYMENT

Provide:

```text
docker compose up
```

as the simplest development deployment.

Initial architecture:

```text
Browser
   ↓
Frontend
   ↓
API
   ↓
PostgreSQL
```

Add worker/cache services only when needed.

Provide production deployment documentation.

---

# 70. CONFIGURATION

Application configuration must be environment-driven.

Example:

```text
DATABASE_URL
SECRET_KEY
CORS_ORIGINS
LOG_LEVEL
CACHE_URL
JOB_QUEUE_URL
FILE_STORAGE_PATH
MAX_UPLOAD_SIZE
QUERY_TIMEOUT
```

Provide:

```text
.env.example
```

Never commit secrets.

---

# 71. TESTING STRATEGY

Implement tests from the beginning.

## Unit tests

Test:

- Query builder
- SQL generation
- Filter parser
- Calculation engine
- Permissions
- Serialization
- Export
- Configuration validation

## Integration tests

Test:

- PostgreSQL
- Dataset creation
- Query execution
- Report creation
- Export
- Authentication

## End-to-end

Test:

```text
Login
 ↓
Create data source
 ↓
Create dataset
 ↓
Create report
 ↓
Create visualization
 ↓
Create dashboard
 ↓
Apply filter
 ↓
Export Excel
```

---

# 72. QUALITY GATES

Before declaring a feature complete:

- Unit tests pass
- Integration tests pass
- E2E tests pass where applicable
- Type checking passes
- Linting passes
- No obvious security vulnerabilities
- API documentation updated
- User documentation updated
- Migration included
- Error handling implemented
- Logging implemented
- Permission checks implemented
- UI responsive
- Accessibility considered

---

# 73. ACCESSIBILITY

Build with accessibility in mind.

Support:

- Keyboard navigation
- Focus management
- Accessible labels
- Semantic HTML
- Screen-reader compatibility
- Sufficient contrast
- Keyboard-accessible report builder

Charts should provide accessible summaries where practical.

---

# 74. RESPONSIVE DESIGN

Support:

- Desktop
- Laptop
- Tablet

Mobile viewing should be supported.

Report editing may prioritize desktop.

---

# 75. REPORT CANVAS ARCHITECTURE

Represent canvas objects as metadata.

Example:

```json
{
  "id": "chart-001",
  "type": "bar",
  "position": {
    "x": 0,
    "y": 0,
    "width": 6,
    "height": 4
  },
  "config": {
    "dataset": "sales",
    "dimension": "country",
    "measure": "revenue"
  }
}
```

This enables:

- Save
- Clone
- Undo
- Redo
- Versioning
- Import
- Export
- Rendering
- Collaboration later

---

# 76. UNDO / REDO

Implement command/history architecture for the report builder.

Users should be able to:

- Undo
- Redo

Avoid tightly coupling undo/redo to individual React components.

---

# 77. AUTOSAVE

Design optional autosave.

Provide:

```text
Saving...
Saved
Unsaved changes
```

Avoid losing user work.

---

# 78. COLLABORATION READINESS

Do not implement full real-time collaboration initially unless required.

However, structure report versions and editing state so future support for:

- Collaborative editing
- Comments
- Presence
- Change tracking

is possible.

---

# 79. DATA SOURCE HEALTH

Create a health-check interface.

Example:

```text
PostgreSQL
Status: Healthy
Latency: 24 ms
Last checked: ...
```

---

# 80. QUERY MONITORING

Provide an administrative query monitor.

Show:

```text
Query ID
User
Dataset
Start time
Duration
Status
Rows
Database
```

Allow cancellation of appropriate running jobs.

---

# 81. ADMIN DASHBOARD

Create an administration dashboard showing:

- Users
- Datasets
- Reports
- Dashboards
- Data sources
- Query activity
- Jobs
- Errors
- System health

---

# 82. DATA GOVERNANCE

Design metadata for:

- Owner
- Description
- Tags
- Classification
- Data sensitivity
- Last refresh
- Source
- Lineage

Example:

```text
Revenue Dataset

Owner: Finance
Source: PostgreSQL
Classification: Internal
Last Refresh: ...
```

---

# 83. DATA LINEAGE

Create architecture for lineage.

Example:

```text
PostgreSQL
   ↓
sales table
   ↓
sales dataset
   ↓
calculated revenue
   ↓
Sales Dashboard
```

Initial implementation may be metadata-based.

---

# 84. DATA CATALOG

Create a searchable catalog of:

- Data sources
- Datasets
- Fields
- Reports
- Dashboards

Each item should have:

- Name
- Description
- Owner
- Tags
- Last updated
- Usage information where available

---

# 85. EXTENSIBILITY RULE

Whenever you encounter a feature that might reasonably be implemented differently by different organizations, prefer:

```text
Interface
+
Configuration
+
Default implementation
```

instead of:

```text
Hard-coded behavior
```

---

# 86. DEVELOPMENT METHODOLOGY

Do not attempt to build the entire platform in one giant implementation.

Build vertically and incrementally.

Every phase must result in a runnable system.

Use the following development phases.

---

# PHASE 0 — ARCHITECTURE

Before writing significant code:

1. Analyze requirements.
2. Identify domain boundaries.
3. Create architecture document.
4. Create database ERD.
5. Define API contracts.
6. Define frontend architecture.
7. Define plugin architecture.
8. Define report JSON schema.
9. Define visualization configuration schema.
10. Define security model.
11. Define deployment architecture.
12. Define testing strategy.

Do not start implementation until these are coherent.

---

# PHASE 1 — PLATFORM FOUNDATION

Build:

- Repository
- Frontend
- Backend
- PostgreSQL
- Docker Compose
- Configuration
- Logging
- Health checks
- Database migrations
- Basic authentication

Deliver a working application.

---

# PHASE 2 — DATA SOURCES

Implement:

- PostgreSQL connector
- CSV
- Excel
- DuckDB where appropriate
- Connection management
- Metadata discovery
- Schema discovery
- Data preview

---

# PHASE 3 — DATASETS

Implement:

- Dataset creation
- Field metadata
- Dimensions
- Measures
- Calculated fields
- Dataset permissions
- Dataset preview

---

# PHASE 4 — QUERY ENGINE

Implement:

- Query specification
- Query validation
- SQL generation
- Filters
- Aggregations
- Sorting
- Pagination
- Query execution
- Error handling

---

# PHASE 5 — VISUALIZATION

Implement:

- Table
- KPI
- Bar
- Line
- Pie
- Area
- Scatter

Create visualization plugin framework.

---

# PHASE 6 — REPORT BUILDER

Implement:

- Canvas
- Drag/drop
- Resize
- Property panel
- Data field panel
- Visualization configuration
- Filters
- Save
- Load
- Undo
- Redo

---

# PHASE 7 — DASHBOARDS

Implement:

- Dashboard creation
- Multiple visualizations
- Layout
- Dashboard filters
- Cross filtering
- Drill down
- Responsive layout

---

# PHASE 8 — EXPORT

Implement:

- Excel
- CSV
- JSON
- PDF if practical

Excel must be production quality.

---

# PHASE 9 — SECURITY

Implement:

- RBAC
- Dataset permissions
- Row-level security architecture
- Column-level security
- Audit logs
- Secure sharing

---

# PHASE 10 — SCHEDULING

Implement:

- Scheduler
- Background jobs
- Scheduled report generation
- Export jobs
- Notifications architecture

---

# PHASE 11 — PLUGINS

Implement:

- Plugin registry
- Plugin manifest
- Plugin lifecycle
- Visualization plugins
- Connector plugins
- Exporter plugins

Document plugin development.

---

# PHASE 12 — EMBEDDING/API

Implement:

- Public API
- Signed embedding
- JavaScript embedding SDK
- API documentation

---

# PHASE 13 — POLISH

Improve:

- Performance
- Accessibility
- UX
- Error messages
- Documentation
- Observability
- Security
- Test coverage

---

# 87. CODING AGENT BEHAVIOR

When working on this project:

## Rule 1

Do not make large architectural changes without explaining why.

## Rule 2

Before modifying a module, inspect existing code and understand its dependencies.

## Rule 3

Do not duplicate functionality.

## Rule 4

Do not create unnecessary abstractions.

## Rule 5

Do not hard-code configuration that belongs in metadata.

## Rule 6

Do not bypass security checks for convenience.

## Rule 7

Do not silently change API contracts.

## Rule 8

Do not introduce proprietary dependencies.

## Rule 9

Prefer small, testable modules.

## Rule 10

Every significant feature must include tests.

---

# 88. AGENT WORK LOOP

For every task:

### Step 1 — Understand

Inspect:

- Existing repository
- Architecture
- Related modules
- Tests
- Documentation

### Step 2 — Plan

Produce a concise implementation plan.

### Step 3 — Implement

Make the smallest coherent change.

### Step 4 — Test

Run relevant tests.

### Step 5 — Review

Check:

- Security
- Performance
- Error handling
- Configuration
- API compatibility

### Step 6 — Document

Update documentation.

### Step 7 — Report

Return:

```text
Implemented:
...

Files changed:
...

Tests:
...

Known limitations:
...

Next recommended step:
...
```

---

# 89. IMPORTANT AGENT RULE

Never pretend that a feature is complete.

If something is incomplete, explicitly state:

```text
Implemented
Partially implemented
Not implemented
```

Do not create fake placeholder functionality and claim it works.

---

# 90. DATABASE MIGRATIONS

Every schema modification must use a migration.

Never manually modify production schema.

Migrations must be:

- Reversible where practical
- Tested
- Versioned

---

# 91. API COMPATIBILITY

Use:

```text
/api/v1/
```

from the beginning.

Do not break existing API consumers without versioning.

---

# 92. BACKWARD COMPATIBILITY

Report definitions and configuration schemas must include versions.

Example:

```json
{
    "schemaVersion": "1.0"
}
```

When changing schemas, create migration functions.

---

# 93. SAMPLE DATA

Create realistic sample datasets.

At minimum:

### Sales

```text
Date
Country
State
City
Product
Category
Customer
Quantity
Revenue
Cost
Profit
```

### HR

```text
Employee
Department
Location
Joining Date
Salary
Performance
```

Use synthetic data only.

---

# 94. DEMO DASHBOARDS

Create example dashboards:

### Sales Dashboard

- Revenue KPI
- Profit KPI
- Revenue trend
- Revenue by country
- Revenue by category
- Top products
- Sales table

### Operations Dashboard

- Orders
- Revenue
- Processing time
- Regional performance

These examples should demonstrate the platform rather than contain hard-coded business logic.

---

# 95. PERFORMANCE TARGETS

Establish measurable targets.

For normal interactive queries:

```text
Target: sub-second to a few seconds
```

depending on database and query complexity.

The UI should remain responsive while queries execute.

Large queries must move to asynchronous execution.

---

# 96. SECURITY REVIEW

Before each major release perform checks for:

- SQL injection
- XSS
- CSRF
- SSRF
- Broken access control
- Privilege escalation
- Insecure direct object references
- File upload vulnerabilities
- Secret leakage
- Formula injection
- Path traversal
- Resource exhaustion

---

# 97. OBSERVABILITY REQUIREMENT

Every important operation should have a correlation/request ID.

Example:

```text
Request ID: 7f82...
Query ID: q-29382
Job ID: job-9238
```

This makes debugging production issues possible.

---

# 98. PRODUCT PHILOSOPHY

The platform should not attempt to replicate every feature of Tableau immediately.

Prioritize:

1. Excellent data connection experience
2. Excellent dataset model
3. Excellent query engine
4. Excellent visual report builder
5. Excellent dashboard experience
6. Excellent Excel export
7. Strong security
8. Extensibility
9. Performance
10. Documentation

Build a strong foundation before adding advanced features.

---

# 99. WHAT NOT TO DO

Do NOT:

- Build everything as one monolithic frontend component
- Put SQL generation in React components
- Put database queries directly in UI code
- Store passwords in plaintext
- Expose database credentials
- Trust frontend permissions
- Execute arbitrary user code
- Hard-code report layouts
- Hard-code visualization behavior
- Use proprietary charting components in the core
- Create unnecessary microservices
- Introduce Kafka/Kubernetes/etc. before they are actually required
- Build an AI chatbot before the core BI platform works
- Implement fake asynchronous jobs
- Ignore migrations
- Ignore tests
- Ignore accessibility
- Ignore open-source licensing

---

# 100. INITIAL MVP DEFINITION

The first production-capable MVP must allow this complete workflow:

```text
User
 ↓
Login
 ↓
Create PostgreSQL connection
 ↓
Discover tables
 ↓
Create Dataset
 ↓
Select fields
 ↓
Define dimensions/measures
 ↓
Create calculated field
 ↓
Create report
 ↓
Drag fields into visualization
 ↓
Create chart
 ↓
Add filters
 ↓
Add multiple charts
 ↓
Save report
 ↓
Create dashboard
 ↓
Apply dashboard filter
 ↓
View interactive dashboard
 ↓
Export to Excel
 ↓
Download Excel
```

This workflow must work end-to-end before calling the MVP complete.

---

# 101. DEFINITION OF DONE

A feature is done only when:

- Implementation exists
- API exists where required
- UI exists where required
- Configuration schema exists
- Database migration exists where required
- Permissions exist
- Error handling exists
- Tests exist
- Documentation exists
- Logging exists
- Security has been considered
- The feature works end-to-end

---

# 102. FINAL ARCHITECTURE GOAL

The final platform should conceptually look like:

```text
                         ┌──────────────────────┐
                         │      BI Platform     │
                         └──────────┬───────────┘
                                    │
              ┌─────────────────────┼─────────────────────┐
              │                     │                     │
        Data Sources            Semantic Layer        Administration
              │                     │                     │
       ┌──────┼──────┐        ┌─────┼─────┐         ┌─────┼─────┐
       │      │      │        │           │         │     │     │
     SQL    Files   APIs   Dimensions   Measures   Users Roles Audit
       │      │      │        │           │
       └──────┼──────┘        └─────┬─────┘
              │                     │
              └──────────┬──────────┘
                         │
                    Query Engine
                         │
                  ┌──────┴──────┐
                  │             │
               Sync          Async
                  │             │
                  └──────┬──────┘
                         │
                  Visualization
                       Engine
                         │
              ┌──────────┼──────────┐
              │          │          │
            Report    Dashboard    Table
              │          │          │
              └──────────┼──────────┘
                         │
                  Export / Share
                         │
             ┌───────────┼───────────┐
             │           │           │
           Excel        PDF        Embed
                         │
                  Future AI Layer
                         │
                  ┌──────┴──────┐
                  │             │
                LLM           Agents
                  │             │
                  └──────┬──────┘
                         │
                   Semantic API
```

---

# 103. FIRST TASK FOR THE CODING AGENT

Do NOT immediately start writing application code.

First:

1. Inspect the current repository.
2. Determine whether a project already exists.
3. Create `docs/architecture.md`.
4. Create `docs/product-requirements.md`.
5. Create `docs/domain-model.md`.
6. Create `docs/api-design.md`.
7. Create `docs/plugin-architecture.md`.
8. Create `docs/report-schema.md`.
9. Create `docs/security.md`.
10. Create the initial database ERD.
11. Propose the repository structure.
12. Propose the technology choices.
13. Identify architectural risks.
14. Define the MVP boundary.
15. Define Phase 1 implementation tasks.

Then STOP and present the architecture and implementation plan.

Do not implement Phase 1 until the architecture has been reviewed.

---

# 104. CONTINUOUS ARCHITECTURAL PRINCIPLE

Throughout development ask:

> "Can this capability be represented as metadata, schema, plugin, policy, or configuration instead of hard-coded application logic?"

If yes, prefer that approach.

The long-term goal is not merely to build a BI application.

The goal is to build a:

# Configurable BI Platform

where organizations and developers can extend the platform without modifying the core system.

The platform should eventually allow:

```text
New Data Source
       ↓
Install Connector

New Chart
       ↓
Install Visualization Plugin

New Export Format
       ↓
Install Export Plugin

New Authentication
       ↓
Install Auth Provider

New Business Function
       ↓
Install Calculation Plugin

New AI Capability
       ↓
Install AI/Agent Plugin
```

The core platform should remain stable while capabilities evolve around it.

---

# FINAL INSTRUCTION

Treat this as a serious open-source software project intended for real users.

Prioritize:

- Correctness
- Security
- Maintainability
- Extensibility
- Performance
- Excellent UX
- Open-source compatibility
- API-first design
- Configuration-driven architecture
- Automated testing
- Documentation

Do not optimize for producing the largest amount of code.

Optimize for producing a platform that can be maintained and extended for many years.

Start with architecture and the MVP vertical slice, then implement incrementally.
