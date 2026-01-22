# Copilot Instructions for teamgit-workshop

## Project Overview
This is a Quarto-based educational travel planning website built for the UCL CHME0039 module. It serves as a learning project for GitHub collaboration and Quarto/Markdown content publishing. The site showcases fictional travellers and destination cities they plan to visit.

## Architecture Overview

### Content Structure (`web_source/`)
- **Core pages**: `index.qmd`, `cities.qmd`, `travellers.qmd` - dashboard pages using Quarto listings
- **Cities**: Hierarchical markdown files under `cities/` organized by continent/country/city (e.g., `cities/africa/egypt/suez.md`)
- **Travellers**: Individual `.qmd` profile files in `travellers/` using Quarto's `about` template with jolla layout
- **Static assets**: `static/` contains web manifest and favicon files
- **Configuration**: `_quarto.yml` defines the website structure, navbar, styling (sandstone theme)

### Dynamic Content Discovery
The site uses Quarto's **listing feature** to auto-discover content:
- `cities.qmd` lists all `cities/**/*.md` files in a table sorted by title with search/filter UI
- `travellers.qmd` lists all `travellers/*.qmd` files in a 3-column grid with images

When adding new cities or travellers, they automatically appear in listings—no manual page registration needed.

## Development Workflow

### Build & Deployment
- **Local rendering**: `quarto render web_source/` from project root
- **Automatic CI/CD**: GitHub Actions workflow (`.github/workflows/publish.yml`) renders and deploys to GitHub Pages on every push to `main`
- **Environment**: Ubuntu runner with R 4.4.2, Python 3.12, renv for R dependencies, pip requirements
- **Build output**: Rendered site goes to `_site/` (from `output-dir: ../_site` in `_quarto.yml`)

### Local Development
- R environment managed via `renv.lock` - use `renv::restore()` to sync dependencies
- Python dependencies in `.devcontainer/requirements.txt`
- Dev container configured for standardized setup

## Content Conventions

### Adding Cities
1. Create markdown file at `web_source/cities/{continent}/{country}/{city}.qmd` (path becomes URL)
2. Use frontmatter with `title:` to display in the cities listing table
3. Standard template:
   ```markdown
   # City Name
   Country: Name (Continent)
   ### What we would like to see there
   - [bullets or description]
   ### Extra information
   - Link to [wikipedia](url)
   ```
4. Mark incomplete sections with `FIXME` comments (placeholder pattern)

### Adding Travellers
1. Create `.qmd` file in `web_source/travellers/{name}.qmd`
2. Use YAML frontmatter with: `title`, `subtitle`, `image` (URL to avatar), `about.template: jolla`
3. Example structure from `gulliver.qmd`:
   ```yaml
   ---
   title: "Name"
   subtitle: "Role/Description"
   image: https://...
   about:
     template: jolla
   ---
   ```
4. Include breadcrumb navigation in HTML block for UX consistency

## File Organization Patterns

- **No configuration needed in code** for new content—Quarto listing paths auto-discover by glob pattern
- **URL structure mirrors filesystem**: `cities/africa/egypt/suez.md` → `cities/africa/egypt/suez/index.html`
- **YAML frontmatter is critical** for metadata that drives listing display (title, subtitle, image fields)
- **Markdown vs Qmd**: Use `.md` for simple city pages, `.qmd` for pages needing rendering logic (like traveller profiles with templates)

## Integration Points

- **GitHub Pages**: Deployed via Actions workflow; configured in repo settings to use Actions as source
- **Quarto themes**: Site uses `sandstone` theme with custom `styles.css` overrides
- **Search**: Built-in Quarto feature; works on rendered titles from frontmatter
- **License**: MIT (see `LICENSE.md`) - referenced in page footer via `license.qmd`

## Key Commands for Editing

- **Preview changes**: Edit content in `web_source/`, run `quarto render web_source/`
- **Add new city**: Create markdown in correct continent folder with `title:` in frontmatter
- **Add new traveller**: Create `.qmd` in `travellers/` with required YAML front matter
- **Test listing changes**: Cities and travellers auto-update in listings after next render—no cache busting needed

## Common Tasks

| Task | File to Edit | Pattern |
|------|--------------|---------|
| Change navbar links | `web_source/_quarto.yml` | Modify `website.navbar.right` section |
| Add/edit theme | `web_source/styles.css` or `_quarto.yml` format theme | CSS overrides or Quarto theme names |
| Update homepage | `web_source/index.qmd` | Standard Quarto markdown syntax |
| Add city | Create `web_source/cities/{continent}/{country}/{city}.md` | Include `title:` in frontmatter |
| Add traveller | Create `web_source/travellers/{name}.qmd` | Use `about.template: jolla` layout |
| Fix deployment | `.github/workflows/publish.yml` | R/Python/Quarto version pins |
