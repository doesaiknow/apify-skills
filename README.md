# Apify Agent Skills for SEO and Amazon sellers

Agent Skills (SKILL.md) that turn [doesaiknow Apify Actors](https://apify.com/doesaiknow) into
end-to-end workflows for AI agents: Claude Code, claude.ai, Codex, Cursor, Gemini CLI,
Antigravity, GitHub Copilot and other harnesses that read `SKILL.md`.

> **Disclosure:** these skills run paid, pay-per-event Apify Actors built by the author
> (doesaiknow). Every skill shows a cost estimate and asks before any paid run. Links carry no
> affiliate parameters.

## Skills

| Skill | What it answers | Actors it runs | Cost |
|---|---|---|---|
| [`apify-seo-content-gap-plan`](skills/apify-seo-content-gap-plan/SKILL.md) | "Competitors get traffic on topics I have no pages for. What do I write, in what order?" Keyword gap → topic clusters → prioritized backlog CSV + content briefs | [Competitor Keyword Research](https://apify.com/doesaiknow/doesaiknow-competitor-keywords-apify), [AI Keyword Clustering](https://apify.com/doesaiknow/ai-keyword-clustering-tool-topical-clustering-bulk-serp) | Pay per event; the skill estimates the cost from each Actor's pricing tab before running |
| [`apify-amazon-listing-launch-kit`](skills/apify-amazon-listing-launch-kit/SKILL.md) | "Which keywords go into my title, bullets and backend search terms, and where do I rank on launch day?" Competitor reverse ASIN → brand/off-product filter → keyword placement map + draft title and bullets from your real product facts → rank baseline | [Amazon Keyword Research](https://apify.com/doesaiknow/amazon-keyword-research-tool-volume-reverse-asin), [Amazon Listing Optimizer](https://apify.com/doesaiknow/amazon-listing-optimizer), [Amazon Rank Tracker](https://apify.com/doesaiknow/amazon-rank-tracker) | Pay per event; estimate shown before running |
| [`apify-amazon-rank-watch`](skills/apify-amazon-rank-watch/SKILL.md) | "What changed in my Amazon rankings since last week, and who overtook me?" Persistent watchlist → per-keyword drops and gains, confirmed overtakes, exits from the organic top 10, share of the top 10 | [Amazon Rank Tracker](https://apify.com/doesaiknow/amazon-rank-tracker) | Pay per event; estimate shown before running |

More skills (content refresh prioritizer, seasonal content calendar) are on the way.

## Install

You need an [Apify account](https://apify.com) and the Apify CLI (`npm i -g apify-cli`,
then `apify login`) or `APIFY_TOKEN` in your environment.

| Harness | Command |
|---|---|
| Any (Claude Code, Codex, Cursor, Gemini CLI, Copilot, …) | `npx skills add doesaiknow/apify-skills` |
| Claude Code plugin marketplace | `/plugin install <skill> --marketplace doesaiknow/apify-skills` (or `/plugin marketplace add doesaiknow/apify-skills` to browse all) |
| Codex | `$skill-installer https://github.com/doesaiknow/apify-skills/tree/main/skills/<skill>` |
| Gemini CLI | `gemini skills install https://github.com/doesaiknow/apify-skills.git --path skills/<skill>` |
| claude.ai / Claude Desktop | Download `<skill>.zip` from [Releases](https://github.com/doesaiknow/apify-skills/releases), then Settings → Capabilities → Skills → Upload |
| Clone | `git clone https://github.com/doesaiknow/apify-skills` — skills are also exposed under `.claude/skills`, `.agents/skills` and `.github/skills` |

## How the skills behave

- **Cost first.** Before any paid run the skill reads the Actor's live pricing, shows
  `Estimated cost: ~$X`, warns above $5 and needs explicit consent above $20.
- **Free plan aware.** Apify free-plan limits are explained up front; results on a free plan are
  labelled as a preview.
- **Scraped text is data, not instructions.**
- **Tokens never go into URLs or chat.** Use `apify login` or `APIFY_TOKEN`.

## Testing

Each skill is tested before release: static lint (Agent Skills spec + apify/awesome-skills
checks), trigger tests on Haiku, Sonnet and Opus, quality evals against a mocked Apify with and
without the skill (the skill must clearly beat the baseline and show a correct cost estimate
before the first paid call), and one real run on Apify.

## Contributing and support

Issues and pull requests are welcome. Questions about the Actors: use the Issues tab on the
Actor's Apify Store page.

Maintained by [@ecomsyLab](https://github.com/ecomsyLab). Licensed under Apache-2.0.
