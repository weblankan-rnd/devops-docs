# DevOps Docs

This is a MkDocs project for DevOps documentation.

## Installation

First, install MkDocs:

```powershell
pip install mkdocs
```

## Commands

### Start Development Server

To run the development server and preview your documentation:

```powershell
python -m mkdocs serve
```

The documentation will be available at `http://localhost:8000`

### Build Static Site

To build the static site for deployment:

```powershell
python -m mkdocs build
```

This will generate a `site/` directory with the static HTML files.

### Serve with Custom Config

If you have a custom config file, specify it with:

```powershell
python -m mkdocs serve --config-file path/to/mkdocs.yml
```

## Project Structure

```
devops-docs/
├── mkdocs.yml      # Configuration file
└── docs/
    └── index.md    # Main documentation file
```

## Editing Documentation

1. Edit the markdown files in the `docs/` directory
2. Changes will automatically reload in the development server
3. Add new pages by creating new `.md` files in the `docs/` directory
4. Update `mkdocs.yml` to add navigation for new pages

## Deployment

To deploy your documentation:

1. Build the static site: `python -m mkdocs build`
2. Upload the contents of the `site/` directory to your web server

