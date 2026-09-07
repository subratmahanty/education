# Server-Driven UI Framework — Coding Agent Prompt

You are a senior software architect and principal full-stack engineer.

Build a production-ready, open-source **Server-Driven UI (SDUI) Framework** using:

- Spring Boot
- React
- PostgreSQL

The primary objective is to create a generic, reusable framework where the UI is driven by server-side configuration rather than page-specific frontend development.

## Core Requirement

The system must allow UI pages to be defined and managed dynamically.

A page should be composed of configurable sections and components. Each section/component can have its own data source and its own data-loading behavior.

The system must support data loading based on events such as:

- On page initialization
- On component/section load
- On user action
- On demand
- On change of another component
- On refresh
- Other configurable events

The React application should be capable of rendering pages dynamically based on the UI definition received from the server.

## Main Objective

Create a framework in which a new page can be created and configured without requiring developers to create a new React page/component implementation for every business requirement.

The framework should allow users to define:

- Pages
- Sections
- Components
- Layouts
- Properties
- Data sources
- Data loading events
- Actions
- Events
- Data bindings
- Validation
- Visibility rules
- Permissions
- Navigation
- Forms
- Tables
- Charts
- Reports

All of these should be configurable and reusable.

## Dynamic UI

The server should provide the UI definition required by the React application.

The React application should interpret the definition and dynamically render the appropriate UI.

The framework should support hierarchical UI structures such as:

Page → Sections → Components → Nested Components

Components should be reusable across multiple pages.

## Data Sources

Each section/component should be able to have one or more configurable data sources.

The framework should be designed to support different types of data sources.

Data should be loaded according to the configuration associated with the component or section.

The system should support passing parameters from other UI components when loading data.

For example:

User selects a country → another component receives the selected country → its data is loaded dynamically.

## Event-Driven UI

The framework should support configurable UI events.

Examples include:

- Page initialization
- Component initialization
- Click
- Change
- Select
- Submit
- Search
- Refresh
- Tab change
- Row selection
- Custom events

Events should be configurable and should be able to trigger one or more actions.

## Actions

The framework should support configurable actions triggered by UI events.

Examples include:

- Load data
- Refresh data
- Navigate
- Open/close UI elements
- Submit
- Reset
- Update values
- Show messages
- Trigger another event
- Execute backend operations

Actions should be reusable and extensible.

## Forms

The framework should support completely configurable forms.

Forms should allow configuration of:

- Fields
- Labels
- Default values
- Validation
- Required fields
- Visibility
- Read-only state
- Dependencies
- Submission behavior
- Actions

A form should be definable through configuration rather than requiring a dedicated React implementation.

## Tables

The framework should provide configurable tables supporting common requirements such as:

- Columns
- Sorting
- Filtering
- Pagination
- Searching
- Row selection
- Row actions
- Inline operations
- Export
- Dynamic data loading

## Charts and Graphical Representation

The framework should support graphical representation of data.

Include configurable chart capabilities such as:

- Bar charts
- Line charts
- Pie charts
- Area charts
- KPI representations
- Other commonly required visualizations

Charts should be configurable and connected to data sources.

## Reports

The framework should support creation of configurable reports.

Reports should be capable of presenting information using:

- Tables
- KPIs
- Charts
- Graphical representations
- Filters
- Multiple sections

Reports should be dynamically generated from the configured UI and data definitions.

## Export

The framework should support exporting report and data information.

At minimum, provide support for:

- Excel
- CSV
- PDF

The export capability should work with dynamically configured reports and tables.

## Reusability

The framework must prioritize maximum reusability.

A component, section, data source, event, or action should be reusable across multiple pages wherever appropriate.

Avoid page-specific implementations wherever a generic framework capability can be used.

## Configuration

The system should allow UI definitions to be created, modified, versioned, and managed.

Changes to UI configuration should not require rebuilding or modifying the React application for every new page.

## Page Lifecycle

The framework should support a page lifecycle in which:

1. A user opens a page.
2. The application obtains the page definition.
3. The UI is rendered dynamically.
4. Components determine when their data needs to be loaded.
5. Configured events are triggered.
6. Configured actions are executed.
7. Data is loaded or refreshed.
8. Components update dynamically.
9. User interactions can trigger additional events and actions.

## Administration

Create the foundation for an administration capability where authorized users can manage:

- Pages
- Sections
- Components
- Layouts
- Data sources
- Events
- Actions
- Permissions
- Versions

The framework should eventually support a visual page builder.

## Versioning

UI definitions should support versioning.

Users should be able to work with different versions of a page and publish an appropriate version.

The system should support safe modification and rollback of published UI definitions.

## Security

The framework must support secure access to pages, components, data, and actions.

Permissions should be configurable and enforced by the backend.

The architecture should prevent unauthorized users from accessing protected data or executing protected actions.

## Extensibility

The framework must be designed as an extensible platform.

It should be possible to add new:

- UI components
- Data source types
- Events
- Actions
- Validation capabilities
- Report capabilities

without redesigning the entire framework.

## Open-Source Quality

Build this as a genuine reusable open-source framework rather than as a demonstration application.

The codebase should be:

- Clean
- Maintainable
- Modular
- Well documented
- Testable
- Extensible
- Production ready

## Demonstration

Create a complete demonstration application showing the framework in action.

The demonstration should contain a realistic page with:

- Multiple sections
- Multiple components
- Data loading on initialization
- Data loading on demand
- User-driven events
- Dynamic data dependencies
- Forms
- Tables
- Charts
- Reports
- Export functionality

The demonstration must use the generic framework capabilities rather than page-specific implementations.

## Development Approach

Before implementing the complete system, understand the requirements carefully and establish a clear implementation plan.

Build the system incrementally.

After each major implementation stage:

- Verify the implementation
- Run tests
- Fix issues
- Ensure existing functionality continues to work
- Keep documentation updated

Do not create unnecessary complexity.

Prioritize a strong, clean foundation that can evolve into a large-scale open-source Server-Driven UI platform.

## Final Goal

The final product should make it possible to build different business applications and dashboards by defining their UI, sections, components, data sources, events, and actions through server-managed configuration.

The fundamental principle is:

**The server defines what the UI should be and how it behaves; React renders and executes that definition; Spring Boot manages the server-side runtime and data; PostgreSQL stores the configurable UI definitions and related metadata.**

Build the framework with this principle at the center of every implementation decision.
