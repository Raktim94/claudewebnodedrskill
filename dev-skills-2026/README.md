# Dev Skills 2026 🚀

Eight senior-developer skills for Claude Code, Claude Desktop (Cowork), and the Claude VS Code extension. Each skill makes Claude produce high-quality, production-grade output automatically.

## Skills included

| Skill | What it does |
|---|---|
| `web-design` | Professional UI/web design — tokens, typography, color, layout, dark mode |
| `system-design` | Scalable architecture — databases, caching, queues, scaling path |
| `cybersecurity` | Secure-by-default code — OWASP, auth, secrets, security reviews |
| `performance-optimization` | Core Web Vitals, bundle size, N+1 queries, caching, profiling |
| `seo` | Technical SEO + AI-search (GEO) + App Store Optimization (ASO) |
| `animation` | Micro-interactions, View Transitions, Motion/GSAP, scroll effects |
| `modern-frontend-2026` | React 19 / Next.js App Router / TypeScript / Tailwind v4 standards |
| `backend-api-design` | Production APIs — REST design, error handling, jobs, observability |

## Install

### Claude Code (CLI) & VS Code extension

Copy the skills into your personal skills folder (available in every project):

```bash
git clone https://github.com/YOUR_USERNAME/dev-skills-2026.git
mkdir -p ~/.claude/skills
cp -r dev-skills-2026/skills/* ~/.claude/skills/
```

Or install for one project only:

```bash
cp -r dev-skills-2026/skills/* your-project/.claude/skills/
```

Restart Claude Code / VS Code. Verify with `/skills` — all eight should appear. The VS Code extension reads the same `~/.claude/skills` folder, so one install covers both.

### Claude Desktop / Cowork

Settings → Capabilities → Skills → upload a skill folder (or zip a folder and upload).

## Usage

Skills trigger automatically — just ask normally:

- "Build me a landing page for my bakery" → `web-design` + `seo` + `animation`
- "Design the backend for a food delivery app" → `system-design` + `backend-api-design`
- "Review my login code" → `cybersecurity`
- "My site is slow" → `performance-optimization`

You can also force one: "Use the system-design skill to plan my app."

## Upload this repo to GitHub

1. Go to https://github.com/new → name it `dev-skills-2026` → Create repository
2. Click "uploading an existing file" → drag the entire folder contents in → Commit
3. Done — install on any machine with the commands above

## Structure

```
dev-skills-2026/
├── README.md
└── skills/
    ├── web-design/SKILL.md
    ├── system-design/SKILL.md
    ├── cybersecurity/SKILL.md
    ├── performance-optimization/SKILL.md
    ├── seo/SKILL.md
    ├── animation/SKILL.md
    ├── modern-frontend-2026/SKILL.md
    └── backend-api-design/SKILL.md
```

MIT licensed — use, modify, share.
