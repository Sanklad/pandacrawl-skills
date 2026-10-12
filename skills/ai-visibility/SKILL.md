---
name: ai-visibility
description: "Find out whether ChatGPT, Claude, Gemini, Perplexity and Google's AI Overviews name a brand for the questions its buyers ask, who they name instead, which sources they trust, and what to change. Use for GEO, AI search visibility, LLM visibility, 'does ChatGPT recommend us', or AI citation work."
---

# PandaCrawl AI Visibility

## Goal

Measure how AI assistants answer the questions a business's buyers ask, then turn that into a short list of changes that make the business more likely to be named and cited. The report answers three things: where the brand is named today, who is named instead and why, and the three changes most likely to move that.

Use this for generative engine optimisation (GEO): "does ChatGPT recommend us", "why does Perplexity cite our competitor", "are we in Google's AI Overviews". For classic rankings use `seo-audit` or `keyword-research`; for one competitor's whole footprint use `competitor-analysis`.

## Required inputs

- The project (`list_projects`; create one with `create_project` if none matches the domain)
- The brand name as buyers would say it, and any other names it goes by

## Project context

The project-context tools are free and shared with the app and other agents.

1. Call `get_project_context` first and ground the work in it — the business and positioning decide which questions matter, and tracking uses the saved competitor list to measure share of voice.
2. This skill needs `business_overview` and competitors. If either is missing, run a minimal inline setup: infer what the business does from the site, propose competitors with `suggest_competitors` (or `find_serp_competitors` on a few seed keywords when Search Console isn't connected), confirm both with the user in one message, and save them with `update_project_context` (`addCompetitors` for the competitors). Never front-load the full interview; suggest `seo-project-setup` at the end for the rest.
3. Before spending credits, check the research log and call `get_ai_visibility`. If tracking already exists with a completed run in the last 30 days, analyse that run instead of starting a new one, and say so.
4. On finish, write back what is durable — the brand's other names, any competitors the AI answers revealed that the user confirms, the pages the report singles out via `addKeyPages` — and append a research log entry: `{ appendResearchLog: { summary: "AI visibility: <brand>, <n> prompts x <models>. Verdict: <conclusion>" } }`.

## Deliver as a report

Deliver through the `seo-report` skill, saving with `skill: "ai-visibility"`. If that skill is not available, say so and stop before writing HTML.

## PandaCrawl MCP tools

- `whoami`: confirm the connection before spending anything. If PandaCrawl is not connected, stop and ask the user to connect it.
- `get_ai_visibility`: free. Current tracking setup, results, mention rate by model, per-prompt status, share of voice, cited sources, trend, and `estimate.perRunUsd` for the next run.
- `suggest_tracked_prompts`: candidate questions with evidence (questions AI already answers about competitors, the project's Search Console searches rewritten as questions, gap-fillers from context). Costs up to about $0.20.
- `setup_prompt_tracking`: brand name, other names, models and schedule. Free.
- `add_tracked_prompts` / `remove_tracked_prompts`: manage the question list. Free; every tracked question costs money on each run.
- `run_prompt_tracking`: asks every question to every chosen model. Spends money: pass the per-run cost the user approved as `maxCostUsd`. Results arrive over a few minutes.
- `suggest_competitors`: proposes search competitors from the site's top Search Console searches when none are saved. One paid call, about $0.01-0.02.
- `get_ranked_keywords` with `resultTypes: ["ai_overview_reference"]`: the searches where Google's AI Overviews cite a domain. Paid; one call per domain.
- `get_bing_index_status`, `inspect_bing_url`: free. Whether key pages are in Bing, whose index also feeds Copilot and ChatGPT search. `list_bing_sites` / `set_bing_site` connect a site if none is set.
- `submit_urls_to_bing`, `setup_indexnow`, `submit_urls_to_indexnow`: free. Get missing pages crawled. Ask the user before submitting.

Keep spend modest: one `suggest_tracked_prompts` call, one tracking run of 8 to 15 questions, and at most three `get_ranked_keywords` calls (the brand and its top two competitors).

## Workflow

1. `whoami`, resolve the project, read project context (see above), and call `get_ai_visibility`.
2. If tracking is not set up, or the user wants new questions: call `suggest_tracked_prompts`, then choose 8 to 15 questions a buyer would really ask. Cover three kinds: category ("best X for Y"), comparison ("X vs Y", "alternatives to Y") and problem ("how do I fix Z"). Include at most one question with the brand's own name, as an accuracy check. Show the list to the user and confirm it before adding anything.
3. `setup_prompt_tracking` (brand, other names, all four models, weekly schedule unless the user asks otherwise), then `add_tracked_prompts`.
4. Call `get_ai_visibility` for `estimate.perRunUsd`, tell the user the cost of one run and of the schedule per month, and wait for approval. Then `run_prompt_tracking` with that amount as `maxCostUsd`. While it runs, do steps 5 and 6. Check `get_ai_visibility` every minute or two until `latestRun.status` is `completed`; do not poll in a tight loop.
5. Google AI Overviews: `get_ranked_keywords` for the brand's domain and its top two competitors with `resultTypes: ["ai_overview_reference"]`. Note which searches cite each, and the ones that cite competitors but not the brand.
6. Bing: if a Bing site is connected, check `get_bing_index_status` and `inspect_bing_url` for the key pages and the pages most relevant to the tracked questions. If none is connected, note it as a recommendation rather than stopping.
7. Analyse the run:
   - Mention rate and average position by model, and the questions where the brand is invisible.
   - Who is named instead, and how often (share of voice).
   - The cited sources, grouped three ways: the brand's own pages, competitors' pages, and third-party pages (review sites, comparison lists, forums, directories, encyclopaedias). Fetch the most-cited pages yourself to see what they actually say.
8. Decide the three changes. Derive them from the data, never from generic advice. Common patterns:
   - The answers cite third-party comparison or review pages that leave the brand out: get listed or reviewed there, with the page and a ready-to-send pitch.
   - A competitor's own page answers the question directly and the brand has no such page: write the plain, honest answer page, naming the question it targets.
   - The brand's relevant pages are not in Bing: submit them and set up IndexNow, with the exact URLs.
   - The brand is named but described wrongly: correct the source the answers cite, and make the brand's own pages state the facts plainly.
9. Review before delivering: run an adversarial pass with a second agent or model if your environment has one, otherwise a fresh self-review. Attack claims beyond the evidence, overconfidence from a single run, and recommendations a small team cannot do.
10. Write and save the report through the `seo-report` skill. Suggest keeping the weekly schedule running and rerunning this skill in a month to compare.

## Output format

`h1`: the report title. Then one or two opening sentences: how often AI names the brand today, and the one change most likely to move it.

If a report template applies (see `seo-report`), its sections and tone replace this list.

Sections in this order:

1. **Verdict** — three to five bullets: overall mention rate, the strongest and weakest model, the biggest gap, and who is named most instead.
2. **Where AI names you** — a table by model (mention rate, average position, cited-as-source rate), then the questions grouped as visible, partial or invisible.
3. **Who gets named instead** — share of voice against competitors, with the questions each one wins.
4. **The sources AI trusts** — the most-cited pages, grouped as yours, competitors' and third-party, each with what it says that the answers repeat.
5. **Google AI Overviews** — the searches where the brand and its competitors are cited, and the gaps.
6. **Indexing** — Bing status for the key pages, or a note that Bing is not connected.
7. **What to do next** — the three changes, each with the exact page, URL or message, in priority order.
8. **How this report was made** — opens with the skill link line from `seo-report`, pointing at `https://pandacrawl.app/docs/skills/ai-visibility` ("PandaCrawl AI Visibility skill"), then the questions asked, the models, the run date and cost, and what you verified by hand.

## Guardrails

- AI answers vary between runs. One run is a sample: say so, prefer patterns across several questions and models, and point to the trend once more runs exist. Never promise that a change will get the brand named.
- Never recommend fake reviews, posing as a customer, astroturfing forums, or buying placements dressed up as editorial. Recommend asking real customers and earning honest coverage.
- Quote cited pages accurately. Check every page you name exists and says what you say it says.
- State costs before spending, in the units the user sees, and never run tracking without approval.
- Tone: calm and plain. No exclamation points, no drama words. Gloss AI Overview, share of voice and IndexNow on first use.
