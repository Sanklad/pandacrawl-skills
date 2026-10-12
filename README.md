# PandaCrawl Skills

Agent Skills for [PandaCrawl](https://pandacrawl.app) — SEO workflows your
coding agent can run against your own data.

Each skill drives the PandaCrawl MCP server, so keyword research, competitor
work, backlinks and audits run on live data from **your** DataForSEO account
rather than the model's guesswork.

## Install

Everything, for whichever agent you use:

```sh
npx skills add Sanklad/pandacrawl-skills --skill '*'
```

Or pick one:

```sh
npx skills add Sanklad/pandacrawl-skills --skill keyword-research
```

Target a specific agent:

```sh
npx skills add Sanklad/pandacrawl-skills --skill '*' --agent claude-code
```

## Skills

| Skill | What it does |
| --- | --- |
| `seo-project-setup` | Sets up a project and points the agent at the right site |
| `keyword-research` | Finds and qualifies keyword opportunities |
| `keyword-clustering` | Groups keywords into topics worth a page each |
| `competitor-analysis` | Digs into one competitor's visibility |
| `competitive-landscape` | Maps who you are actually competing with |
| `link-prospecting` | Finds realistic link targets |
| `local-seo` | Local pack and map visibility work |
| `seo-audit` | Runs a crawl and reads the findings back |
| `seo-coach` | Talks through strategy with your data in hand |
| `ai-visibility` | Checks whether ChatGPT, Claude, Gemini, Perplexity and Google's AI Overviews name you, and what to change (GEO) |
| `seo-report` | Writes and saves each workflow's finished report; the other skills deliver through it |

## Prerequisites

Connect the PandaCrawl MCP server first — the URL and an API key are on the
**Connect your AI** page inside the app. Skills call that server, so without it they
have nothing to read.

## Licence

MIT; see `LICENSE`.
