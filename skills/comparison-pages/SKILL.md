---
name: comparison-pages
description: "Plan, fact-check and write honest comparison and alternative pages ('X alternative', 'X vs Y', 'best X tools', 'X pricing') that can rank in Google and get cited by AI assistants, then publish, submit and track them. Use when someone asks for comparison, alternative, 'vs' or competitor pages, how to win searches for a bigger competitor's name, or how to show up when buyers ask AI which tool to pick."
---

# PandaCrawl Comparison Pages

## Goal

People who search "<competitor> alternative" or "<competitor> vs <brand>" are close to buying. Turn that demand into a small set of pages that deserve to rank: the comparisons a buyer actually searches for, with every competitor fact checked against the vendor's own pages, and an honest account of when the competitor is the better pick. AI assistants quote pages like these when buyers ask which tool to choose, so the same pages serve search and AI answers.

The result is three to five finished pages (not a long list of ideas), live on the site, submitted for indexing, and tracked.

For measuring how AI answers mention the brand today, use `ai-visibility`. For the pages that cite the brand on other sites, use `link-prospecting`.

## Required inputs

- The project (`list_projects`; create one with `create_project` if none matches the domain)
- What the brand sells and to whom, and its prices and plans as published on its own site

## Project context

The project-context tools are free and shared with the app and other agents.

1. Call `get_project_context` first and ground the work in it: the saved competitors are the comparison candidates, and positioning says where the brand honestly wins.
2. This skill needs `business_overview` and competitors. If either is missing, run a minimal inline setup: infer what the business does from its site, propose competitors with `suggest_competitors` (or `find_serp_competitors` on a few seed keywords when Search Console isn't connected), confirm both with the user in one message, and save them with `update_project_context`. Never front-load the full interview; suggest `seo-project-setup` at the end for the rest.
3. Before spending credits, check the research log. If comparison research ran within the last 30 days, reuse it and say so.
4. On finish, write back what is durable: each published page via `addKeyPages` (role `money`), and a research log entry: `{ appendResearchLog: { summary: "Comparison pages: <pages>. Target searches: <terms with volume>. Verdict: <conclusion>" } }`.

## Deliver as a report

Deliver the plan and results through the `seo-report` skill, saving with `skill: "comparison-pages"`. The pages themselves are delivered as files or drafts (see "Publish"). If `seo-report` is not available, say so and stop before writing HTML.

## PandaCrawl MCP tools

- `whoami`: confirm the connection before spending anything.
- `get_search_console_performance`: free. Queries that already mention a competitor, and how new the site is (few impressions means aim at specific terms, not head terms).
- `get_keyword_metrics`: volume, difficulty and intent for up to 700 candidate terms in one call. Paid, a few cents.
- `get_serp_results`: who ranks for a term today. Up to 10 terms per call, about 5 credits each at the default depth. Use `depth: 10` here.
- `get_ranked_keywords`: optional, the comparison terms a competitor or a rival comparison page already ranks for.
- `create_rank_tracker`, `add_rank_tracking_keywords`: track the target terms once pages are live. Show the cost with `estimate_rank_tracker_cost` first.
- `add_tracked_prompts`, `get_ai_visibility`: track the buyer questions the pages answer in AI assistants, if tracking is set up (otherwise suggest `ai-visibility`).
- `setup_indexnow`, `submit_urls_to_indexnow`, `submit_urls_to_bing`: free. Get new pages crawled. `inspect_urls` checks Google indexing later.

Keep spend modest: one `get_keyword_metrics` call and one or two `get_serp_results` calls.

You also need a web fetch or browser tool to check competitor prices on their own pages. If you have none, mark every competitor figure as unverified in the draft and tell the user before anything is published.

## Workflow

1. **Find the demand.** For each saved competitor and the category, build candidate terms: "<competitor> alternative(s)", "<competitor> vs <brand>", "<competitor> pricing", "cheap/free <competitor> alternative", "best <category> tools", "<category> for <audience>", and integration or feature terms the brand genuinely fits (for example "<competitor> MCP"). Add Search Console queries that already contain a competitor's name. Score them all in one `get_keyword_metrics` call.
2. **Check who ranks.** Run `get_serp_results` on the eight to ten strongest candidates. Judge winnability from the results, not the difficulty score alone:
   - small independent sites, forums and dated listicles in the top ten mean a new, well-made page can compete;
   - results owned entirely by the competitor's own pages and docs mean the term is navigational: don't build a page for it; cover it inside a broader page instead.
3. **Choose three to five pages**, one per intent, never one per keyword variant. Usual set: one page per major competitor ("<competitor> alternative: <brand> vs <competitor>"), one category comparison ("best <category> tools" or "<category> tools compared"), and a hub page that links them. Confirm the list with the user before writing.
4. **Collect the facts.**
   - Competitor prices, plan names, limits and add-ons: from the vendor's own pricing, docs or help pages, opened today. Record the URL and date for each. Note the currency the page showed; if it wasn't the reader's, say so rather than converting silently. If two of the vendor's own pages disagree, say that too. Anything you can't confirm is marked unverified and left out of tables.
   - The brand's own prices, limits and per-action costs: from its pricing page or the user. Never estimate the brand's own numbers.
   - One worked example per page: a realistic month for the target buyer, priced on both tools from the facts you collected. Show the arithmetic in your notes and check it twice.
5. **Write each page** to the template below. Lead with the answer, keep claims verifiable, and give the competitor's real strengths their own section. If the brand only wins in some situations, say which.
6. **On-page and AI readiness**, for each page:
   - title of 60 characters or fewer with the main term near the start; meta description of 160 or fewer; one H1; a short, lowercase slug such as `/compare/<competitor>-alternative`;
   - a dated line saying when competitor facts were checked, and that the brand wrote the page;
   - an FAQ of three or four real questions, shown on the page and marked up as `FAQPage`, plus `Article` and `BreadcrumbList` structured data;
   - links: hub to every page, every page to the others, from the site footer or navigation, and from the pricing page where it fits.
   - Site-wide, check once: the pages are in the sitemap; the site has an `llms.txt` summarising what it is, what it costs and linking the comparisons; the home page has `Organization` and `SoftwareApplication` (or `Product`) structured data. Add what's missing if you can edit the site; otherwise list it as a next step.
7. **Publish.** If you can edit the site (a repository, CMS or site builder you have access to), add the pages there, preview them, and ask the user before anything goes live. If you can't, deliver each page as a ready-to-paste draft with its title, description, slug, FAQ and structured data.
8. **Submit and track** once the pages are live:
   - `setup_indexnow` (once per site) then `submit_urls_to_indexnow` with the new and changed URLs, or `submit_urls_to_bing` if IndexNow can't be verified. Tell the user Google has no submission API: request indexing in Search Console's URL Inspection, or wait for the sitemap.
   - Add the target terms to a rank tracker, after showing the cost.
   - If AI visibility tracking is set up, propose adding two or three buyer questions the pages answer ("What is the best cheaper alternative to <competitor>?") with `add_tracked_prompts`, after the user agrees.
9. **Review before delivering.** Run an adversarial pass with a second agent or model if available, otherwise a fresh self-review: every competitor number against its source, every claim about the brand against its own site, the arithmetic in each worked example, and anything that reads as a hit piece.
10. **Report** through `seo-report`: the pages and their target terms with volumes, what's live, what was submitted, what's tracked, and when to recheck (four to eight weeks in Search Console, and the next AI visibility runs).

## Page template

1. **H1**: "<competitor> alternative: <brand> vs <competitor>" (or the category title).
2. **The short answer**: two or three sentences naming who should pick each tool.
3. **Price**: a table of entry plan, the plan that includes the feature buyers compare on, other options, free plan or trial, and discounts. Cite the vendor's pricing page under it.
4. **What each plan includes**: a table of the limits buyers compare (projects or sites, tracked items, usage limits, the key feature, integrations).
5. **A worked month**: the realistic example, priced on both tools, and how the answer changes at higher volume.
6. **Where <competitor> is the better pick**: real strengths, stated plainly.
7. **Where <brand> is the better pick**: specific, provable reasons.
8. **Moving from <competitor>**: three or four concrete steps.
9. **Questions people ask**: the FAQ.
10. **Call to action**: one line, matching the site's main offer.

## Output format

`h1`: "Comparison pages for <brand>". Then one or two opening sentences: how many pages, the searches they target, and what's live.

If a report template applies (see `seo-report`), its sections and tone replace this list.

1. **Verdict**: the pages built and the single most valuable one.
2. **The searches**: a table of term, monthly searches, difficulty, who ranks today, and the page that targets it. Terms left out, and why.
3. **The pages**: for each, its URL (or draft), title, description, target terms, and the facts it relies on with source links and dates.
4. **Site-wide changes**: sitemap, `llms.txt`, structured data, internal links.
5. **Submitted and tracked**: URLs submitted and where, rank tracker terms, AI questions added.
6. **What to do next**: in order, including Search Console indexing requests, outreach to the independent lists that rank (suggest `link-prospecting`), and a date to re-check prices.
7. **How this report was made**: opens with the skill link line from `seo-report`, pointing at `https://pandacrawl.app/docs/skills/comparison-pages` ("PandaCrawl Comparison Pages skill"), then the tools used, the date facts were checked, and what was verified by hand.

## Guardrails

- Never invent or round up a competitor's weakness, and never guess a price. Unverified facts stay out of the pages.
- No near-duplicate pages per keyword variant, city or competitor with nothing new to say. Each page must answer something the current top results don't, such as dated prices, a worked example or an honest verdict. If it can't, don't publish it.
- Fair to competitors: name their real strengths. A page that reads as a hit piece loses readers and gets quoted less by AI assistants.
- Don't copy text from competitor pages or rival comparison lists; summarise in your own words and link the source.
- Ask before publishing to a live site or submitting URLs, and before any paid tracking.
- Prices change. Put the check date on every page, and tell the user to re-check them every three months.
- Tone: calm and plain. No exclamation points or superlatives about the brand.
