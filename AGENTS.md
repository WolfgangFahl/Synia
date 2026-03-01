# Synia - Architecture and Agent Documentation

## Overview

Synia is a single-page JavaScript application for exploring and visualizing Wikidata through dynamic SPARQL queries. The application uses a template-based system where view definitions are stored as Wikidata pages and rendered dynamically.

**Source**: https://synia.toolforge.org/

**Citation**: Finn Årup Nielsen, Daniel Mietchen, Egon Willighagen (2017). "Scholia, Scientometrics and Wikidata". pp. 237-259. DOI: 10.1007/978-3-319-70407-4_36

## Architecture Pattern

Synia follows a **Template-Driven Single Page Application** architecture:

1. **Hash-based Routing**: Uses URL fragments (`#aspect/Q123`) to determine what to display
2. **Template Retrieval**: Fetches view templates from Wikidata wiki pages
3. **SPARQL Execution**: Executes embedded SPARQL queries against Wikidata Query Service
4. **Dynamic Rendering**: Renders results as DataTables or embedded visualizations

## Core Components

### 1. Configuration Module (`js/config.js`)

**Purpose**: Central configuration for Wikidata endpoints and services

**Key Settings**:
- `queryServiceUrl`: Wikidata SPARQL endpoint (https://query-main.wikidata.org)
- `wikiUrl`: Wikidata main URL
- `namespace`: Template namespace (`Wikidata:Synia:`)
- `endpoint`: Full SPARQL endpoint URL
- `templateApiUrl`: MediaWiki API for fetching templates

**Agent Responsibility**: Configuration management, endpoint resolution

### 2. Routing & Parsing Module (`js/synia.js`)

**Core Functions**:

#### `hashToAspect(hash)`
**Purpose**: Parse URL hash to determine view type ("aspect")

**Patterns Matched**:
- `#aspect/Q123/aspect2/Q456` → "aspect-aspect2" (two-entity view)
- `#aspect/Q123/aspect2` → "aspect-aspect2-index" (entity + index view)
- `#aspect/Q123` → "aspect" (single-entity view)
- `#aspect` → "aspect-index" (index view)

**Agent Type**: Router/Parser

#### `hashToQ(hash)` / `hashToQQ(hash)`
**Purpose**: Extract Wikidata entity IDs (Q123, L456) from URL hash

**Returns**:
- Single entity ID or null
- Array of two entity IDs [Q1, Q2] or null

**Agent Type**: Entity Extractor

### 3. Template Processing Module

#### `aspectToTemplateUrl(aspect)`
**Purpose**: Convert aspect name to MediaWiki API URL for template retrieval

**Process**:
1. Takes aspect name (e.g., "author")
2. Constructs MediaWiki API query
3. Returns URL to fetch template from `Wikidata:Synia:{aspect}`

**Agent Type**: URL Generator

#### Template Parsing (Main Execution Flow)
**Purpose**: Parse and render wiki templates containing SPARQL queries

**Template Syntax Supported**:
- `=Header 1=` → `<h1>`
- `==Header 2==` → `<h2>`
- `===Header 3===` → `<h3>`
- `----` → `<hr>`
- `{{SPARQL|query=...}}` → Execute and render SPARQL

**Regex Patterns**:
```javascript
reTemplateParts = /(=[^=]+?=|==[^=]+?==|===.+?===|\-\-\-\-|{{SPARQL\s+.+?^}})/gms
reSparqlTemplate = /{{SPARQL\s*\|(\s*endpoint\s*=\s*(.*?)\s*\|)?\s*query\s*=(.+?)^}}/gms
```

**Agent Type**: Template Parser/Renderer

### 4. SPARQL Execution Module

#### `sparqlTemplateToSparql(sparqlTemplate, q, q2)`
**Purpose**: Interpolate entity IDs into SPARQL template queries

**Process**:
1. Converts `{{!}}` to `|` (wikitext pipe escape)
2. Replaces `PREFIX target: <http://.../Q1234>` with actual entity ID
3. Handles single-target or dual-target queries

**Agent Type**: Query Template Processor

#### `sparqlToDataTable(sparql, element, options)`
**Purpose**: Execute SPARQL query and render as interactive DataTable

**Process**:
1. POST SPARQL query to endpoint
2. Parse JSON response
3. Convert to DataTable format
4. Handle column transformations (Label/Url pairs)
5. Render with pagination, sorting, filtering
6. Add "Query Service" link to view query

**Options**:
- `endpoint`: Custom SPARQL endpoint
- `paging`: Enable/disable pagination
- `linkPrefixes`/`linkSuffixes`: URL transformations

**Agent Type**: Query Executor + Data Visualizer

#### `sparqlDataToSimpleData(response)`
**Purpose**: Convert verbose SPARQL JSON to simplified format

**Input**: `{head: {vars: [...]}, results: {bindings: [...]}}`
**Output**: `{data: [{key: value}], columns: [...]}`

**Agent Type**: Data Transformer

### 5. Data Transformation Module

#### `convertDataTableData(data, columns, linkPrefixes, linkSuffixes)`
**Purpose**: Transform SPARQL results for DataTables rendering

**Key Transformations**:
1. **Label Handling**: Merge `itemLabel` into `item` column
2. **URL Handling**: Convert `item` + `itemUrl` into clickable links
3. **Description Handling**: Rename `itemDescription` to "item description"
4. **Link Generation**: 
   - External links: `<a href="http://...">Label</a>`
   - Internal links: `<a onclick="window.location.hash='...'; reload()">Label</a>`

**Agent Type**: Data Presenter

#### `capitalizeFirstLetter(string)`
**Purpose**: Format column headers

**Agent Type**: Formatter

#### `escapeHTML(html)`
**Purpose**: Sanitize HTML output (security)

**Agent Type**: Security Sanitizer

### 6. Rendering Module

**Rendering Strategies**:

1. **Table Rendering** (default):
   - Uses DataTables library
   - Supports sorting, filtering, pagination
   - Multi-language support (Danish, German)

2. **Graph Rendering** (when SPARQL contains `#defaultView:`):
   - Embeds Wikidata Query Service visualization
   - Renders as responsive iframe
   - Supports various visualization types (map, timeline, graph, etc.)

**Agent Type**: View Renderer

### 7. Internationalization Module

#### `entityToLabel(entity, language)`
**Purpose**: Extract entity labels with language fallback

**Fallback Chain**: requested language → English → da/de/es/fr/jp/nl/no/ru/sv/zh → entity ID

**Agent Type**: i18n Handler

#### User Language Detection
```javascript
let userLang = navigator.language || navigator.userLanguage;
```

**Supported DataTable Languages**: Danish (da), German (de-DE)

**Agent Type**: Locale Detector

## Application Flow

```
1. User navigates to URL with hash (#author/Q42)
   ↓
2. hashToAspect() → "author"
   hashToQ() → "Q42"
   ↓
3. aspectToTemplateUrl() → Fetch from Wikidata:Synia:author
   ↓
4. Parse template:
   - Headers → <h1>, <h2>, <h3>
   - {{SPARQL}} blocks → Extract queries
   ↓
5. For each SPARQL block:
   - sparqlTemplateToSparql() → Replace Q1234 with Q42
   - sparqlToDataTable() → Execute query
   ↓
6. Render results:
   - Check for #defaultView → iframe visualization
   - Otherwise → DataTable with sorting/filtering
   ↓
7. Add "Query Service" link for debugging
```

## Key Design Patterns

### 1. Convention over Configuration
- URL hash structure determines view type
- Template naming follows predictable pattern
- No explicit routing configuration needed

### 2. Template-Driven Views
- View logic stored in Wikidata wiki pages
- Non-developers can create new views
- SPARQL queries embedded in templates
- Separation of presentation and data

### 3. Progressive Enhancement
- Basic table rendering always works
- Enhanced visualizations via iframe embedding
- Graceful degradation if templates missing

### 4. Client-Side Only
- No backend required
- All processing in browser
- Direct SPARQL endpoint access
- Static file hosting sufficient

## Extension Points

### Adding New Views
1. Create new page at `Wikidata:Synia:{aspect-name}`
2. Add headers and SPARQL blocks
3. Navigate to `#aspect-name` or `#aspect-name/Q123`

### Custom Endpoints
Templates can specify custom SPARQL endpoints:
```
{{SPARQL|endpoint=https://custom.wikibase.cloud/query/sparql|query=...}}
```

### Adding New Visualizations
Include `#defaultView:` directive in SPARQL:
```sparql
#defaultView:Map
SELECT ?item ?itemLabel ?coord WHERE {
  ...
}
```

## Dependencies

### External Libraries
- **jQuery 3.6.4**: DOM manipulation and AJAX
- **Bootstrap**: UI components and responsive layout
- **DataTables**: Interactive table rendering

### External Services
- **Wikidata Query Service**: SPARQL endpoint
- **MediaWiki API**: Template retrieval
- **Wikidata.org**: Entity data and templates

## File Structure

```
synia/
├── index.html           # Entry point, loads libraries and scripts
├── js/
│   ├── config.js        # Configuration agent
│   └── synia.js         # Core application logic
├── css/
│   └── synia.css        # Minimal custom styles
└── libs/                # Third-party libraries (not downloaded)
    ├── jquery/
    ├── bootstrap/
    └── datatables/
```

## Agent Communication Patterns

1. **Router → Parser**: Hash → Aspect + Entity IDs
2. **Parser → Template Loader**: Aspect → Template URL → Template Content
3. **Template Parser → Query Processor**: Template → SPARQL queries
4. **Query Processor → Executor**: SPARQL template + Entity IDs → Concrete query
5. **Executor → Transformer**: SPARQL JSON → Simple data structure
6. **Transformer → Renderer**: Data + Columns → HTML table/iframe

## Potential Improvements

### Architecture
- Add client-side caching of templates
- Implement service worker for offline access
- Add state management (Redux/similar)
- Modularize code with ES6 modules

### Features
- Add breadcrumb navigation
- Implement search functionality
- Add template editor UI
- Support for URL query parameters
- Add analytics/telemetry

### Performance
- Lazy load DataTables library
- Implement virtual scrolling for large datasets
- Add query result caching
- Optimize regex parsing

### Developer Experience
- Add TypeScript types
- Implement unit tests
- Add build system (webpack/vite)
- Create template validation tool
- Add debugging mode with verbose logging

## Security Considerations

- HTML escaping for user content (`escapeHTML()`)
- CORS configured for API access
- POST method for SPARQL to prevent URL injection
- No server-side execution (client-side only)

## License & Attribution

See CITATION.cff for citation information.

Based on the Scholia project for Wikidata bibliometrics and scientometrics visualization.
