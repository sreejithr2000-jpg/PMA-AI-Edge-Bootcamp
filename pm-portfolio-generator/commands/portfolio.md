---
description: Generate a PM portfolio HTML page from a product repo's documentation and source code
argument-hint: <repo-path> [--output <output-path>]
allowed-tools: Read, Write, Glob, Grep, Bash, Skill, Agent
---

# Generate PM Portfolio

You are generating a professional PM product portfolio as a single standalone HTML page.

## Parse Arguments

- `$ARGUMENTS` contains the repo path and optional `--output` flag
- If no `--output` is specified, save to `<repo-path>/portfolio.html`
- If `$ARGUMENTS` is empty, ask the user for the repo path

## Step 1: Load the Skill

Use the Skill tool to invoke `portfolio-generator`. This will load the full generation instructions.

## Step 2: Explore the Repository

Read the following files from the target repo (skip any that don't exist):

1. **README.md** — product overview, tech stack, quick start
2. **Any file matching** `*PRODUCT*`, `*PRD*`, `*BRIEF*`, `*SPEC*` — problem statement, personas, features, user stories
3. **Any file matching** `*ARCHITECTURE*`, `*TECHNICAL*`, `*DESIGN*` — system design, data flow, tech decisions
4. **package.json** or **requirements.txt** or **Cargo.toml** or **go.mod** — tech stack confirmation
5. **Main source directory** (`app/`, `src/`, `lib/`) — scan structure to understand features
6. **DECISIONS.md**, **ISSUES.md**, **CHANGELOG.md** — context on trade-offs and decisions

Use Glob to find these files, then Read the most relevant ones (up to 10 files).

## Step 3: Extract Portfolio Content

From the files read, identify and extract:

1. **Product Name & Tagline**
2. **Problem Statement** — core pain points the product solves
3. **User Personas** — who the product is built for (roles, goals, pain points)
4. **User Journey & Pain Points** — step-by-step flow from problem to solution
5. **User Stories** — "As a [persona], I want [action] so that [outcome]"
6. **Features** — key capabilities with metrics where available
7. **Tech Stack** — frameworks, databases, APIs, deployment
8. **Architecture** — system layers, data flow, key components

## Step 4: Generate HTML Portfolio

Follow the design system and template structure from the portfolio-generator skill to generate a single, self-contained HTML file with:

- Embedded CSS (no external stylesheets except Google Fonts)
- 7 sections: Hero, Problem, Personas, Journey, Stories, Features, Architecture
- Scroll-reveal animations via IntersectionObserver
- Sticky navigation with active section highlighting
- Responsive design (desktop + tablet + mobile)
- Pure HTML/CSS architecture diagram (no images)
- Professional PM portfolio aesthetic

## Step 5: Save and Report

1. Write the HTML file to the output path
2. Tell the user the file path and suggest opening it in a browser
