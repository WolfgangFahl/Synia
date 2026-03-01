# Synia - Wikidata Explorer

A single-page JavaScript application for exploring and visualizing Wikidata through dynamic SPARQL queries.

## Overview

Synia is a template-driven web application that allows users to explore Wikidata entities through customizable views. The application fetches view templates from Wikidata wiki pages and executes embedded SPARQL queries to display interactive visualizations.

**Live Version**: https://synia.toolforge.org/

## Features

- **Hash-based Routing**: Navigate using URL fragments (e.g., `#author/Q42`)
- **Dynamic Templates**: View definitions stored as Wikidata wiki pages
- **SPARQL Visualizations**: Interactive tables and embedded visualizations
- **Multi-language Support**: Supports Danish, German, and other languages
- **Client-side Only**: No backend required, runs entirely in the browser

## Quick Start

### 1. Install Dependencies

First, fetch the required JavaScript libraries:

```bash
bash scripts/getlibs
```

### 2. Serve the Application

Since this is a static single-page app, you can serve it with any web server:

```bash
# Using Python 3
python3 -m http.server 8000

# Using Python 2
python -m SimpleHTTPServer 8000

# Using Node.js (if you have http-server installed)
npx http-server

# Using PHP
php -S localhost:8000
```

### 3. Open in Browser

Navigate to `http://localhost:8000` in your web browser.

## Usage Examples

### Viewing an Entity

Navigate to a specific aspect and entity:
- `#author/Q42` - View Douglas Adams as an author
- `#venue/Q5412157` - View a publication venue
- `#organization/Q95` - View Google as an organization

### Viewing Indexes

View index pages for aspects:
- `#author` - Browse authors index
- `#venue` - Browse venues index

### Two-Entity Views

Compare or relate two entities:
- `#author/Q42/topic/Q42` - View an author's relationship to a topic

## Architecture

Synia follows a **Template-Driven Single Page Application** architecture:

1. **Hash-based Routing**: URL fragments determine what to display
2. **Template Retrieval**: Fetches view templates from `Wikidata:Synia:{aspect}` pages
3. **SPARQL Execution**: Executes embedded queries against Wikidata Query Service
4. **Dynamic Rendering**: Renders results as DataTables or embedded visualizations

For detailed architecture documentation, see [AGENTS.md](AGENTS.md).

## File Structure

```
synia/
├── index.html           # Entry point
├── js/
│   ├── config.js        # Configuration (endpoints, namespaces)
│   └── synia.js         # Core application logic
├── css/
│   └── synia.css        # Custom styles
├── libs/                # Third-party libraries (fetched by scripts/getlibs)
│   ├── jquery/
│   ├── bootstrap/
│   └── datatables/
├── scripts/
│   └── getlibs          # Script to download dependencies
├── AGENTS.md            # Architecture documentation
├── CITATION.cff         # Citation information
└── README.md            # This file
```

## Dependencies

### Runtime Dependencies

- **jQuery 3.6.4**: DOM manipulation and AJAX
- **Bootstrap 5.x**: UI components and responsive layout
- **DataTables**: Interactive table rendering

### External Services

- **Wikidata Query Service**: SPARQL endpoint (https://query-main.wikidata.org)
- **MediaWiki API**: Template retrieval from Wikidata wiki pages

## Creating Custom Views

You can create custom views by adding new templates to Wikidata:

1. Create a new page at `Wikidata:Synia:{your-aspect-name}`
2. Add headers and SPARQL blocks using the template syntax:

```wikitext
=Main Header=

==Section Header==

{{SPARQL|query=
SELECT ?item ?itemLabel WHERE {
  # Your SPARQL query here
}
}}
```

3. Navigate to `#{your-aspect-name}` or `#{your-aspect-name}/Q123`

### Template Syntax

- `=Header=` - Level 1 header
- `==Header==` - Level 2 header
- `===Header===` - Level 3 header
- `----` - Horizontal rule
- `{{SPARQL|query=...}}` - SPARQL query block
- `{{SPARQL|endpoint=...|query=...}}` - Custom endpoint SPARQL query

### Entity ID Substitution

Templates use PREFIX substitution for entity IDs:
- `PREFIX target: <http://www.wikidata.org/entity/Q1234>` - Single entity
- `PREFIX target1: <...>` and `PREFIX target2: <...>` - Two entities

## Development

### Configuration

Edit `js/config.js` to change endpoints:

```javascript
queryServiceUrl = "https://query-main.wikidata.org";
wikiUrl = "https://www.wikidata.org";
namespace = "Wikidata:Synia:";
```

### Customization

The application is designed to be extended:

- **Add custom endpoints**: Specify in SPARQL templates
- **Add visualizations**: Use `#defaultView:` directives (Map, Timeline, Graph, etc.)
- **Customize styling**: Edit `css/synia.css`

## Browser Support

Works in all modern browsers that support:
- ES6 JavaScript features
- Fetch API
- CSS Grid/Flexbox

## Security

- HTML escaping for user content
- CORS configured for API access
- POST method for SPARQL queries to prevent URL injection
- Client-side only execution (no server-side code)

## Citation

If you use Synia in your research, please cite:

```bibtex
@inproceedings{nielsen2017scholia,
  title={Scholia, Scientometrics and Wikidata},
  author={Nielsen, Finn {\AA}rup and Mietchen, Daniel and Willighagen, Egon},
  year={2017},
  pages={237--259},
  doi={10.1007/978-3-319-70407-4_36}
}
```

See [CITATION.cff](CITATION.cff) for machine-readable citation information.

## License

Based on the Scholia project for Wikidata bibliometrics and scientometrics visualization.

## Related Projects

- **Scholia**: https://scholia.toolforge.org/
- **Wikidata**: https://www.wikidata.org/
- **Wikidata Query Service**: https://query.wikidata.org/

## Contributing

To contribute:

1. Fork the repository
2. Create your feature branch
3. Test your changes locally
4. Submit a pull request

## Support

For issues and questions:
- Check existing Wikidata:Synia templates for examples
- Visit the Wikidata Query Service for SPARQL help
- Review the architecture documentation in [AGENTS.md](AGENTS.md)

## Acknowledgments

Thanks to the Wikidata community and the Scholia project contributors.
