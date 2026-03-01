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

### 2. Open in Browser

Simply open `index.html` directly in your web browser:

```bash
# On macOS
open index.html

# On Linux
xdg-open index.html

# On Windows
start index.html
```

Or just double-click `index.html` in your file manager.

**Note**: A web server is **not required** because Synia queries external APIs (Wikidata) that support CORS. However, if you prefer to use a web server:

```bash
# Using Python 3
python3 -m http.server 8000
# Then navigate to: http://localhost:8000
```

## Available Templates

There are currently **173 Wikidata:Synia template pages** available:
- **50 single-entity views** (e.g., `#author/Q42`)
- **112 index pages** (e.g., `#author-index`)
- **11 two-entity relationship views** (e.g., `#author/Q42/topic/Q11660`)

[Browse all 173 Synia templates on Wikidata](https://www.wikidata.org/w/index.php?title=Special:PrefixIndex&prefix=Synia%3A&namespace=4)

## Usage Examples

### Popular Single-Entity Views

Navigate to a specific aspect and entity:

- **Author**: [Douglas Adams](https://synia.toolforge.org/#author/Q42) - `#author/Q42`
  - Template: [Wikidata:Synia:author](https://www.wikidata.org/wiki/Wikidata:Synia:author)
- **Venue**: [Nature journal](https://synia.toolforge.org/#venue/Q180445) - `#venue/Q180445`
  - Template: [Wikidata:Synia:venue](https://www.wikidata.org/wiki/Wikidata:Synia:venue)
- **Organization**: [Google](https://synia.toolforge.org/#organization/Q95) - `#organization/Q95`
  - Template: [Wikidata:Synia:organization](https://www.wikidata.org/wiki/Wikidata:Synia:organization)
- **Topic**: [Artificial Intelligence](https://synia.toolforge.org/#topic/Q11660) - `#topic/Q11660`
  - Template: [Wikidata:Synia:topic](https://www.wikidata.org/wiki/Wikidata:Synia:topic)
- **Work**: [The Hitchhiker's Guide to the Galaxy](https://synia.toolforge.org/#work/Q25169) - `#work/Q25169`
  - Template: [Wikidata:Synia:work](https://www.wikidata.org/wiki/Wikidata:Synia:work)
- **Language**: [English](https://synia.toolforge.org/#language/Q1860) - `#language/Q1860`
  - Template: [Wikidata:Synia:language](https://www.wikidata.org/wiki/Wikidata:Synia:language)
- **Country**: [Denmark](https://synia.toolforge.org/#country/Q35) - `#country/Q35`
  - Template: [Wikidata:Synia:country](https://www.wikidata.org/wiki/Wikidata:Synia:country)

### Index Pages

Browse collections of entities:

- **Authors Index**: [Browse all authors](https://synia.toolforge.org/#author-index) - `#author-index`
  - Template: [Wikidata:Synia:author-index](https://www.wikidata.org/wiki/Wikidata:Synia:author-index)
- **Venues Index**: [Browse all venues](https://synia.toolforge.org/#venue-index) - `#venue-index`
  - Template: [Wikidata:Synia:venue-index](https://www.wikidata.org/wiki/Wikidata:Synia:venue-index)
- **Topics Index**: [Browse all topics](https://synia.toolforge.org/#topic-index) - `#topic-index`
  - Template: [Wikidata:Synia:topic-index](https://www.wikidata.org/wiki/Wikidata:Synia:topic-index)

### Two-Entity Relationship Views

Explore connections between entities:

- **Author-Topic**: [Douglas Adams & Artificial Intelligence](https://synia.toolforge.org/#author/Q42/topic/Q11660) - `#author/Q42/topic/Q11660`
  - Template: [Wikidata:Synia:author-topic](https://www.wikidata.org/wiki/Wikidata:Synia:author-topic)
- **Venue-Topic**: [Nature & Genetics](https://synia.toolforge.org/#venue/Q180445/topic/Q7162) - `#venue/Q180445/topic/Q7162`
  - Template: [Wikidata:Synia:venue-topic](https://www.wikidata.org/wiki/Wikidata:Synia:venue-topic)
- **Country-Topic**: [Denmark & Wind Power](https://synia.toolforge.org/#country/Q35/topic/Q42511) - `#country/Q35/topic/Q42511`
  - Template: [Wikidata:Synia:country-topic](https://www.wikidata.org/wiki/Wikidata:Synia:country-topic)

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

## License

This project is licensed under the GNU General Public License v2.0 - see the [LICENSE](LICENSE) file for details.

## Original Project

This is a redistribution of [Synia](https://github.com/fnielsen/synia) by Finn Årup Nielsen, originally deployed at https://synia.toolforge.org/.

Synia is based on the [Scholia project](https://scholia.toolforge.org/) for Wikidata bibliometrics and scientometrics visualization.

### What's Different in This Repository

This repository adds:
- **Comprehensive documentation** (README.md, AGENTS.md)
- **Automated library download script** (scripts/getlibs)
- **Citation metadata** (CITATION.cff)
- **Pre-downloaded dependencies** for offline/local use

**The core application code (JavaScript, CSS, HTML) remains unchanged from the original.**

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
