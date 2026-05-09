---
name: PM Portfolio Generator
description: This skill should be used when the user asks to "generate a portfolio", "create a PM portfolio", "build a product showcase page", "make a portfolio HTML", "create a product case study", or needs to convert a code repository's documentation into a polished single-page HTML portfolio showcasing problem statement, personas, user journey, features, and architecture.
version: 1.0.0
---

# PM Portfolio Generator

Generate a professional, single-page HTML portfolio for any PM product by analyzing its repository. The output is a standalone HTML file suitable for PM interviews, case study presentations, or product showcases.

## Information Extraction

Before generating, read the repo to extract these 8 elements:

| Element | Where to Look | Fallback |
|---------|--------------|----------|
| Product Name | README.md title, package.json name | Repo folder name |
| Tagline | README.md first line after title | Derive from problem statement |
| Problem Statement | PRD, PRODUCT_BRIEF, README "why" section | Infer from features |
| User Personas | PRD personas section, user research docs | Derive from target users mentioned anywhere |
| User Journey | PRD user flows, journey maps | Construct from feature sequence |
| User Stories | PRD user stories, issues/tickets | Generate from features + personas |
| Features | README features list, PRD core features, source code structure | Scan app/src directories |
| Architecture | TECHNICAL_ARCHITECTURE, system design docs, tech stack in README | Infer from package.json + directory structure |

## HTML Template Structure

The portfolio uses a single HTML file with embedded `<style>` and `<script>`. No external dependencies except Google Fonts.

### Section 1: Hero
- Product name as large heading
- Tagline in italic
- 1-sentence value proposition
- 3-4 key stats (sources, users, questions, etc.)
- Full-viewport height, dark gradient background

### Section 2: Problem Statement
- Section label + title + subtitle
- 3-4 pain point cards in a grid
- Each card: icon (HTML entity), title, description
- White background section

### Section 3: User Personas
- 3 persona cards (Primary, Secondary, Extended)
- Each card: tag, name, description, 3 goals as bullet list
- Color-coded by persona priority (coral, blue, purple)

### Section 4: User Journey & Pain Points
- 3-4 horizontal steps connected by arrows
- Each step: number badge, title, description, pain point below dashed line
- Responsive: stacks vertically on mobile

### Section 5: User Stories
- 4-5 story cards with left coral border
- Each card: "As a [role]" label, "I want [action]" heading, "So that [outcome]" description

### Section 6: Features & Demo
- 3-5 feature cards in a grid
- Each card: badge (CORE/AI/UX), icon, title, description, key metric
- White background section

### Section 7: Technical Architecture
- Pure HTML/CSS architecture diagram showing system layers
- Layer structure: Data Pipeline -> Processing -> Storage -> Application -> User
- Each component is a colored box with border
- Arrows between layers using HTML entities
- Tech stack badges at the bottom
- 3 insight cards: Performance, Scalability, and a third relevant insight

## Design System

### CSS Variables
```css
:root {
  --navy: #1a1a2e;
  --coral: #e94560;
  --cream: #f8f6f2;
  --text: #2d2d3a;
  --text-light: #6b6b7b;
  --accent-blue: #4a90d9;
  --accent-green: #2ecc71;
  --accent-purple: #9b59b6;
  --accent-orange: #f39c12;
  --radius: 16px;
}
```

### Typography
- Import Google Fonts: `DM Serif Display` (headings) + `DM Sans` (body)
- Section labels: 0.75rem, uppercase, letter-spacing 0.15em, coral color
- Section titles: 2.5rem, DM Serif Display, navy
- Body text: 0.92rem, DM Sans, text-light color

### Cards
- White background, 16px border-radius, subtle shadow
- Hover: translateY(-4px) + stronger shadow
- 1px border at rgba(0,0,0,0.04)

### Animations
- `.reveal` class: opacity 0, translateY(30px) initially
- IntersectionObserver adds `.visible` class on scroll
- Delay classes: `.reveal-delay-1` through `.reveal-delay-3`

### Architecture Diagram Colors
- Scrapers/Pipeline: orange theme
- AI/Processing: purple theme
- Database/Storage: green theme
- Application: blue theme
- Infrastructure: coral theme
- User: navy theme

### Responsive Breakpoints
- Desktop: full grid layouts
- Tablet (< 900px): 2-column grids, vertical journey flow
- Mobile (< 600px): single column, stacked stats

## Quality Checklist

Before outputting the HTML, verify:
- [ ] All content is grounded in actual repo documentation (no fabricated features)
- [ ] No external image dependencies (use HTML entities for icons)
- [ ] Single self-contained HTML file
- [ ] Architecture diagram accurately reflects the actual tech stack
- [ ] Responsive design at all breakpoints
- [ ] Navigation links match section IDs
- [ ] Scroll animations work via IntersectionObserver
- [ ] Footer credits the author

## Reference

See `references/html-template-structure.md` for the complete HTML skeleton and CSS template that can be adapted for any product.
