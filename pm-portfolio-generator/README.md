# PM Portfolio Generator

A Claude Code plugin that generates professional, single-page HTML portfolios for PM products by analyzing any code repository.

## What It Does

Point it at any product repository and get a polished portfolio page with:
- **Problem Statement** — core pain points the product solves
- **User Personas** — who the product is built for
- **User Journey** — step-by-step flow with pain points mapped
- **User Stories** — structured "As a / I want / So that" cards
- **Features & Demo** — key capabilities with metrics
- **Technical Architecture** — pure HTML/CSS system diagram

## Installation

### Option 1: Install from GitHub

```bash
claude /install-plugin https://github.com/shuboya1030/pm-portfolio-generator
```

### Option 2: Local Installation

1. Clone this repo:
```bash
git clone https://github.com/shuboya1030/pm-portfolio-generator.git
```

2. Add to your Claude Code settings (`~/.claude/settings.json`):
```json
{
  "plugins": {
    "pm-portfolio-generator": {
      "source": {
        "source": "directory",
        "path": "/path/to/pm-portfolio-generator"
      }
    }
  }
}
```

## Usage

### Slash Command

```
/portfolio /path/to/your/product/repo
```

With custom output path:
```
/portfolio /path/to/repo --output /path/to/output.html
```

### What the Plugin Reads

The plugin scans your repo for:
- `README.md` — product overview and tech stack
- `*PRODUCT*`, `*PRD*`, `*BRIEF*` files — problem statement, personas, features
- `*ARCHITECTURE*`, `*TECHNICAL*` files — system design and data flow
- `package.json` / `requirements.txt` — tech stack confirmation
- `app/` or `src/` directory structure — feature understanding

## Output

A single, self-contained HTML file with:
- Embedded CSS (no external dependencies except Google Fonts)
- Scroll-reveal animations
- Sticky navigation with active section highlighting
- Responsive design (desktop + tablet + mobile)
- Pure HTML/CSS architecture diagram
- Professional PM portfolio aesthetic

## Requirements

- Claude Code with Read, Write, Glob, and Grep tool access

## License

MIT
