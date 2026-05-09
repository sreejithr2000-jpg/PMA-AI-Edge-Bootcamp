# HTML Template Structure Reference

This is the reusable HTML skeleton for generating PM portfolio pages. Adapt the content for each product while keeping the structure and design system consistent.

## Complete HTML Skeleton

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>{{PRODUCT_NAME}} - Product Portfolio</title>
  <link href="https://fonts.googleapis.com/css2?family=DM+Serif+Display&family=DM+Sans:wght@400;500;700&display=swap" rel="stylesheet">
  <style>
    /* === CSS Variables === */
    :root {
      --navy: #1a1a2e;
      --navy-light: #16213e;
      --coral: #e94560;
      --coral-light: #ff6b6b;
      --cream: #f8f6f2;
      --cream-dark: #eee9e0;
      --text: #2d2d3a;
      --text-light: #6b6b7b;
      --accent-blue: #4a90d9;
      --accent-green: #2ecc71;
      --accent-purple: #9b59b6;
      --accent-orange: #f39c12;
      --shadow: 0 4px 20px rgba(26, 26, 46, 0.08);
      --shadow-hover: 0 8px 30px rgba(26, 26, 46, 0.15);
      --radius: 16px;
    }

    * { margin: 0; padding: 0; box-sizing: border-box; }
    html { scroll-behavior: smooth; }
    body {
      font-family: 'DM Sans', -apple-system, sans-serif;
      color: var(--text); background: var(--cream); line-height: 1.7;
      overflow-x: hidden;
    }
    h1, h2, h3, h4 { font-family: 'DM Serif Display', serif; font-weight: 400; }

    /* === Navigation === */
    nav {
      position: fixed; top: 0; width: 100%; z-index: 100;
      background: rgba(248, 246, 242, 0.92); backdrop-filter: blur(12px);
      border-bottom: 1px solid rgba(26, 26, 46, 0.06); padding: 0 2rem;
    }
    nav.scrolled { box-shadow: 0 2px 20px rgba(0,0,0,0.06); }
    .nav-inner {
      max-width: 1200px; margin: 0 auto;
      display: flex; align-items: center; justify-content: space-between; height: 64px;
    }
    .nav-logo { font-family: 'DM Serif Display'; font-size: 1.25rem; color: var(--navy); text-decoration: none; }
    .nav-logo span { color: var(--coral); }
    .nav-links { display: flex; gap: 2rem; list-style: none; }
    .nav-links a {
      text-decoration: none; color: var(--text-light); font-size: 0.85rem;
      font-weight: 500; letter-spacing: 0.03em; text-transform: uppercase;
      transition: color 0.2s; position: relative;
    }
    .nav-links a::after {
      content: ''; position: absolute; bottom: -4px; left: 0;
      width: 0; height: 2px; background: var(--coral); transition: width 0.3s;
    }
    .nav-links a:hover, .nav-links a.active { color: var(--navy); }
    .nav-links a:hover::after, .nav-links a.active::after { width: 100%; }

    /* === Sections === */
    section { padding: 6rem 2rem; }
    .container { max-width: 1100px; margin: 0 auto; }
    .section-label {
      font-family: 'DM Sans'; font-size: 0.75rem; font-weight: 700;
      letter-spacing: 0.15em; text-transform: uppercase; color: var(--coral);
      margin-bottom: 0.75rem;
    }
    .section-title { font-size: 2.5rem; color: var(--navy); margin-bottom: 1rem; line-height: 1.2; }
    .section-subtitle { font-size: 1.1rem; color: var(--text-light); max-width: 640px; margin-bottom: 3rem; }

    /* === Hero === */
    .hero {
      min-height: 100vh; display: flex; align-items: center; justify-content: center;
      background: linear-gradient(135deg, var(--navy) 0%, var(--navy-light) 50%, #0f3460 100%);
      position: relative; overflow: hidden; text-align: center; padding: 2rem;
    }
    .hero::before {
      content: ''; position: absolute; width: 600px; height: 600px;
      background: radial-gradient(circle, rgba(233,69,96,0.15), transparent 70%);
      top: -100px; right: -100px; border-radius: 50%;
    }
    .hero-content { position: relative; z-index: 1; max-width: 800px; }
    .hero-badge {
      display: inline-block; padding: 0.4rem 1.2rem; border-radius: 50px;
      background: rgba(233,69,96,0.15); color: var(--coral-light);
      font-size: 0.8rem; font-weight: 700; letter-spacing: 0.1em; text-transform: uppercase;
      margin-bottom: 2rem;
    }
    .hero h1 { font-size: 3.8rem; color: #fff; margin-bottom: 1.5rem; line-height: 1.15; }
    .hero h1 span { color: var(--coral-light); }
    .hero-tagline { font-size: 1.3rem; color: rgba(255,255,255,0.7); font-style: italic; max-width: 560px; margin: 0 auto 1rem; }
    .hero-desc { font-size: 1.05rem; color: rgba(255,255,255,0.55); max-width: 600px; margin: 0 auto 2.5rem; }
    .hero-stats { display: flex; gap: 3rem; justify-content: center; margin-top: 2rem; }
    .hero-stat .number { font-family: 'DM Serif Display'; font-size: 2rem; color: var(--coral-light); }
    .hero-stat .label { font-size: 0.8rem; color: rgba(255,255,255,0.5); text-transform: uppercase; letter-spacing: 0.08em; }

    /* === Cards === */
    .cards-grid { display: grid; gap: 1.5rem; }
    .cards-grid.col-2 { grid-template-columns: repeat(2, 1fr); }
    .cards-grid.col-3 { grid-template-columns: repeat(3, 1fr); }
    .cards-grid.col-4 { grid-template-columns: repeat(4, 1fr); }
    .card {
      background: #fff; border-radius: var(--radius); padding: 2rem;
      box-shadow: var(--shadow); transition: transform 0.3s, box-shadow 0.3s;
      border: 1px solid rgba(0,0,0,0.04);
    }
    .card:hover { transform: translateY(-4px); box-shadow: var(--shadow-hover); }
    .card-icon {
      width: 52px; height: 52px; border-radius: 14px;
      display: flex; align-items: center; justify-content: center;
      font-size: 1.5rem; margin-bottom: 1.25rem;
    }
    .card h3 { font-size: 1.2rem; color: var(--navy); margin-bottom: 0.6rem; }
    .card p { font-size: 0.92rem; color: var(--text-light); line-height: 1.65; }

    /* === Icon Background Colors === */
    .icon-red { background: rgba(233,69,96,0.1); color: var(--coral); }
    .icon-blue { background: rgba(74,144,217,0.1); color: var(--accent-blue); }
    .icon-orange { background: rgba(243,156,18,0.1); color: var(--accent-orange); }
    .icon-purple { background: rgba(155,89,182,0.1); color: var(--accent-purple); }
    .icon-green { background: rgba(46,204,113,0.1); color: var(--accent-green); }

    /* === Journey === */
    .journey-flow { display: flex; align-items: stretch; gap: 0; margin-top: 2rem; }
    .journey-step {
      flex: 1; text-align: center; padding: 2rem 1.5rem;
      background: var(--cream); border-radius: var(--radius); position: relative; margin: 0 1.2rem;
    }
    .journey-step::after {
      content: '\2192'; position: absolute; right: -1.6rem; top: 50%;
      transform: translateY(-50%); font-size: 1.5rem; color: var(--coral); font-weight: 700;
    }
    .journey-step:last-child::after { display: none; }
    .journey-num {
      width: 36px; height: 36px; border-radius: 50%;
      background: var(--coral); color: #fff; font-weight: 700;
      display: inline-flex; align-items: center; justify-content: center;
      font-size: 0.85rem; margin-bottom: 1rem;
    }
    .journey-pain {
      margin-top: 1rem; padding-top: 0.75rem; border-top: 1px dashed rgba(233,69,96,0.3);
      font-size: 0.78rem; color: var(--coral); font-weight: 500;
    }

    /* === User Stories === */
    .story-card {
      background: #fff; border-radius: var(--radius); padding: 1.5rem 2rem;
      border-left: 4px solid var(--coral); margin-bottom: 1rem; box-shadow: var(--shadow);
    }
    .story-as { font-size: 0.75rem; font-weight: 700; color: var(--coral); text-transform: uppercase; letter-spacing: 0.08em; }
    .story-want { font-size: 1rem; color: var(--navy); font-weight: 500; margin: 0.3rem 0; }
    .story-so { font-size: 0.88rem; color: var(--text-light); }

    /* === Architecture Diagram === */
    .arch-diagram {
      background: var(--cream); border-radius: var(--radius); padding: 3rem 2rem; margin-top: 2rem;
    }
    .arch-layer { display: flex; align-items: center; justify-content: center; gap: 1rem; margin-bottom: 1.5rem; }
    .arch-box {
      padding: 0.8rem 1.2rem; border-radius: 10px; text-align: center;
      font-size: 0.8rem; font-weight: 500; min-width: 120px; border: 2px solid;
    }
    .arch-scraper { background: rgba(243,156,18,0.08); border-color: var(--accent-orange); color: var(--accent-orange); }
    .arch-ai { background: rgba(155,89,182,0.08); border-color: var(--accent-purple); color: var(--accent-purple); }
    .arch-db { background: rgba(46,204,113,0.08); border-color: var(--accent-green); color: var(--accent-green); }
    .arch-app { background: rgba(74,144,217,0.08); border-color: var(--accent-blue); color: var(--accent-blue); }
    .arch-user { background: rgba(26,26,46,0.06); border-color: var(--navy); color: var(--navy); }
    .arch-arrow { text-align: center; font-size: 1.2rem; color: var(--text-light); margin: 0.5rem 0; }
    .arch-arrow-label { font-size: 0.68rem; color: var(--text-light); font-weight: 500; }
    .arch-connector { display: flex; align-items: center; gap: 0.4rem; color: var(--text-light); font-size: 1rem; }
    .tech-badges { display: flex; flex-wrap: wrap; gap: 0.6rem; margin-top: 2rem; justify-content: center; }
    .tech-badge {
      padding: 0.35rem 0.85rem; border-radius: 50px; font-size: 0.75rem;
      font-weight: 600; background: #fff; color: var(--navy); border: 1px solid rgba(26,26,46,0.1);
    }

    /* === Insights === */
    .insights-grid { display: grid; grid-template-columns: repeat(3, 1fr); gap: 1.5rem; margin-top: 2rem; }
    .insight-card { background: var(--cream); border-radius: var(--radius); padding: 1.5rem; border-left: 3px solid; }

    /* === Animations === */
    .reveal { opacity: 0; transform: translateY(30px); transition: opacity 0.7s ease, transform 0.7s ease; }
    .reveal.visible { opacity: 1; transform: translateY(0); }
    .reveal-delay-1 { transition-delay: 0.1s; }
    .reveal-delay-2 { transition-delay: 0.2s; }
    .reveal-delay-3 { transition-delay: 0.3s; }

    /* === Responsive === */
    @media (max-width: 900px) {
      .cards-grid.col-4 { grid-template-columns: repeat(2, 1fr); }
      .cards-grid.col-3 { grid-template-columns: 1fr; }
      .hero h1 { font-size: 2.5rem; }
      .journey-flow { flex-direction: column; gap: 1rem; }
      .journey-step { margin: 0; }
      .journey-step::after { content: '\2193'; right: 50%; top: auto; bottom: -1.2rem; transform: translateX(50%); }
      .journey-step:last-child::after { display: none; }
      .arch-layer { flex-wrap: wrap; }
      .insights-grid { grid-template-columns: 1fr; }
      .nav-links { display: none; }
    }
    @media (max-width: 600px) {
      .cards-grid.col-4, .cards-grid.col-2 { grid-template-columns: 1fr; }
      .hero-stats { flex-direction: column; gap: 1.5rem; }
      section { padding: 4rem 1.25rem; }
    }
  </style>
</head>
<body>

<!-- Navigation -->
<nav id="navbar">
  <div class="nav-inner">
    <a href="#hero" class="nav-logo">{{NAV_LOGO}}</a>
    <ul class="nav-links">
      <li><a href="#problem">Problem</a></li>
      <li><a href="#personas">Personas</a></li>
      <li><a href="#journey">Journey</a></li>
      <li><a href="#stories">Stories</a></li>
      <li><a href="#features">Features</a></li>
      <li><a href="#architecture">Architecture</a></li>
    </ul>
  </div>
</nav>

<!-- Section 1: Hero -->
<section class="hero" id="hero">
  <div class="hero-content">
    <div class="hero-badge">PM Portfolio</div>
    <h1>{{PRODUCT_NAME}}</h1>
    <p class="hero-tagline">"{{TAGLINE}}"</p>
    <p class="hero-desc">{{VALUE_PROPOSITION}}</p>
    <div class="hero-stats">
      <!-- 3-4 stats: <div class="hero-stat"><div class="number">X</div><div class="label">Label</div></div> -->
    </div>
  </div>
</section>

<!-- Section 2: Problem Statement (white bg) -->
<section style="background:#fff;" id="problem">
  <div class="container">
    <div class="section-label reveal">Problem Statement</div>
    <h2 class="section-title reveal">{{PROBLEM_TITLE}}</h2>
    <p class="section-subtitle reveal">{{PROBLEM_SUBTITLE}}</p>
    <div class="cards-grid col-4">
      <!-- 3-4 pain point cards -->
    </div>
  </div>
</section>

<!-- Section 3: User Personas (cream bg) -->
<section id="personas">
  <div class="container">
    <div class="section-label reveal">User Personas</div>
    <h2 class="section-title reveal">Who we're building for</h2>
    <div class="cards-grid col-3">
      <!-- 3 persona cards -->
    </div>
  </div>
</section>

<!-- Section 4: User Journey (white bg) -->
<section style="background:#fff;" id="journey">
  <div class="container">
    <div class="section-label reveal">User Journey & Pain Points</div>
    <h2 class="section-title reveal">{{JOURNEY_TITLE}}</h2>
    <div class="journey-flow reveal">
      <!-- 3-4 journey steps -->
    </div>
  </div>
</section>

<!-- Section 5: User Stories (cream bg) -->
<section id="stories">
  <div class="container">
    <div class="section-label reveal">User Stories</div>
    <h2 class="section-title reveal">What users need to accomplish</h2>
    <div class="reveal">
      <!-- 4-5 story cards -->
    </div>
  </div>
</section>

<!-- Section 6: Features (white bg) -->
<section style="background:#fff;" id="features">
  <div class="container">
    <div class="section-label reveal">Features & Demo</div>
    <h2 class="section-title reveal">{{FEATURES_TITLE}}</h2>
    <div class="cards-grid col-3">
      <!-- 3-5 feature cards -->
    </div>
  </div>
</section>

<!-- Section 7: Architecture (cream bg for diagram, with insights) -->
<section id="architecture">
  <div class="container">
    <div class="section-label reveal">Technical Architecture</div>
    <h2 class="section-title reveal">System design & data flow</h2>
    <div class="arch-diagram reveal">
      <!-- Architecture diagram: layers of arch-box elements connected by arch-arrow divs -->
      <!-- Tech badges at bottom -->
    </div>
    <!-- 3 insight cards -->
    <div class="insights-grid">
      <!-- insight-card elements -->
    </div>
  </div>
</section>

<!-- Footer -->
<footer style="text-align:center;padding:3rem 2rem;background:var(--navy);color:rgba(255,255,255,0.4);font-size:0.85rem;">
  <p>{{PRODUCT_NAME}} &mdash; Product Portfolio</p>
</footer>

<script>
  // Scroll reveal
  const observer = new IntersectionObserver((entries) => {
    entries.forEach(entry => {
      if (entry.isIntersecting) entry.target.classList.add('visible');
    });
  }, { threshold: 0.1, rootMargin: '0px 0px -40px 0px' });
  document.querySelectorAll('.reveal').forEach(el => observer.observe(el));

  // Navbar scroll effect
  const nav = document.getElementById('navbar');
  window.addEventListener('scroll', () => nav.classList.toggle('scrolled', window.scrollY > 50));

  // Active nav link
  const sections = document.querySelectorAll('section[id]');
  const navLinks = document.querySelectorAll('.nav-links a');
  window.addEventListener('scroll', () => {
    let current = '';
    sections.forEach(s => { if (window.scrollY >= s.offsetTop - 100) current = s.getAttribute('id'); });
    navLinks.forEach(l => l.classList.toggle('active', l.getAttribute('href') === '#' + current));
  });
</script>
</body>
</html>
```

## Architecture Diagram Pattern

For the architecture diagram section, follow this layered pattern. Adapt the boxes and labels to match the actual tech stack:

```html
<!-- Layer Label -->
<div style="text-align:center;margin-bottom:0.5rem;">
  <span style="font-size:0.7rem;font-weight:700;color:var(--accent-orange);letter-spacing:0.1em;text-transform:uppercase;">
    {{LAYER_NAME}}
  </span>
</div>

<!-- Components in this layer -->
<div class="arch-layer">
  <div class="arch-box arch-scraper">Component A<br><small>Tech</small></div>
  <div class="arch-connector">&rarr;</div>
  <div class="arch-box arch-ai">Component B<br><small>Tech</small></div>
</div>

<!-- Arrow to next layer -->
<div class="arch-arrow">&darr;<br><span class="arch-arrow-label">Data description</span></div>
```

### Color Mapping for Architecture Boxes
- **Data ingestion** (scrapers, ETL): `arch-scraper` (orange)
- **AI/ML processing**: `arch-ai` (purple)
- **Storage** (databases, caches): `arch-db` (green)
- **Application** (web frameworks, APIs): `arch-app` (blue)
- **End users**: `arch-user` (navy)

## Persona Card Pattern

```html
<div class="card persona-card persona-primary">
  <span class="persona-tag">Primary User</span>
  <h3>{{PERSONA_NAME}}</h3>
  <p>{{PERSONA_DESCRIPTION}}</p>
  <ul class="persona-goals">
    <li>{{GOAL_1}}</li>
    <li>{{GOAL_2}}</li>
    <li>{{GOAL_3}}</li>
  </ul>
</div>
```

Use `persona-primary` (coral), `persona-secondary` (blue), `persona-tertiary` (purple) for the three tiers.
