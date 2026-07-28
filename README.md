# gossip-girl

A reusable [Claude Code](https://claude.com/claude-code) plugin that turns technical news into decision-ready briefings, delivered in the voice of the Gossip Girl narrator. Invoked as `/gossip-girl:brief`.

Given a topic or a set of recent developments, it produces a briefing a busy technical leader can act on in two minutes: what happened, why it matters, and what to do or watch next, wrapped in an omniscient, faintly dishy narrator voice that opens with "Spotted:" and signs off "XOXO, Gossip Girl."

The premise, and the discipline: **the voice is a delivery layer, never a substitute for accuracy.** Strip the persona away and an intelligent reader should still learn something true and useful from every item.

---

## What it does

- **Takes a topic or pasted developments.** Hand it `/gossip-girl:brief this week in AI agents` and it gathers the news itself; paste in a pile of links and it briefs those.
- **Triangulates before it cites.** For every candidate it finds the primary source, confirms against one independent outlet, cites the primary, and grades the item (confirmed / single-source rumor / conflicting). Aggregators are treated as leads, not sources.
- **Enforces a decision-ready format.** A punchy `Spotted:` headline, 3 to 6 items each carrying What / Why it matters / What's next, and an `XOXO` close that names the single most important takeaway.
- **Learns what the reader cares about.** It keeps a running tally of the themes the reader leans into, both from in-conversation reactions and, optionally, from what they forward, reply to, save, or react to in other channels, and weights future briefings toward them.

## Use cases

Where a signature-voice briefing earns its keep. The through-line: someone has to read a pile of technical news and decide what to do, and the voice makes them actually read it.

- **The Monday "what did I miss" scan.** A technical leader hands it `this week in AI agents` and gets a triangulated, act-on-it-in-two-minutes briefing instead of forty open tabs.
- **Team newsletter or Slack digest.** Turn a week of releases, papers, and funding rounds into a recurring internal briefing people look forward to reading, because it has a voice, without sacrificing the sourcing.
- **Competitive and market watch.** Point it at a competitor, a protocol, or a category (`MCP spec changes`, `agent-governance startups`) and get the moves framed as what they are: positioning, land-grabs, retreats.
- **Investor or exec update prep.** Brief a space you are about to walk into a meeting on, with primary-source citations already attached so nothing on your slide is unverified.
- **Conference and launch-day triage.** During a firehose week (a big release, a spec drop), feed it the day's developments and get the three that actually matter, graded by how well they are sourced.
- **Personalized standing brief.** Over repeated runs it learns the themes a given reader leans into and weights toward them, so the briefing gets sharper the more it is used.
- **Onboarding a new hire to a fast-moving space.** A skimmable, opinionated map of "here is what happened and why it matters" beats a reading list of thirty links.

Not the right tool when you need exhaustive coverage, legal or investment advice, or a neutral wire report. It is a decision aid with a point of view, deliberately concise.

## Design principles

1. **Accuracy first.** No invented developments, numbers, names, or quotes. Unconfirmed items are labeled in voice ("even I couldn't verify this one, darlings"), never stated as fact.
2. **Substance over style.** Every item carries real technical content and a clear "so what." If an item has no substance, it gets cut rather than dressed up.
3. **Skimmable, not a soap opera.** One line per beat, varied metaphors, one term of endearment per item. A briefing wearing a cocktail dress.
4. **The voice is studied, not winged.** The skill loads a register (omniscient narrator, social-scandal framing, the Spotted/XOXO scaffolding) and calibrated example lines before writing, so tone stays consistent across runs.
5. **House style: no em dashes.** En dashes or commas throughout.

## How the rigor actually shows up

From the included [example run](examples/this-week-in-ai-agents.md), the triangulation pass caught three things a style-first tool would have missed:

- A funding valuation that aggregators had **wrong** ($1.0B vs the verified $1.2B).
- A widely-cited stat that was actually **a year stale**, reframed honestly instead of passed off as fresh.
- A launch with **only a vendor announcement** behind it, flagged as such with a "test it yourself" caveat.

## Repository layout

This repo is a self-contained Claude Code plugin. The plugin root is the repo root (it holds `.claude-plugin/plugin.json`).

```
gossip-girl/
├── .claude-plugin/
│   └── plugin.json                    plugin manifest (name, description, version, author)
├── README.md                          this file
├── TESTING.md                         test plan: core paths, edge cases, the learning loop
├── skills/
│   └── brief/                         the skill; invocation is /gossip-girl:brief
│       ├── SKILL.md                   workflow, triangulation, format, style rules
│       └── references/
│           ├── voice.md               calibrated example lines that keep the tone consistent
│           └── reader-interests.example.md   cold-start template for the personalization memory
├── examples/
│   └── this-week-in-ai-agents.md      a real triangulated run, with the sourcing notes kept in
└── .gitignore                         excludes the per-install personal memory (reader-interests.md)
```

The invocation name comes from the layout: plugin `gossip-girl` + skill folder `brief` = `/gossip-girl:brief`.

## Install

### Option A – try it locally (no marketplace)

Clone the repo, then load it with `--plugin-dir`:

```bash
git clone https://github.com/dot-naomi/gossip-girl.git
claude --plugin-dir ./gossip-girl
```

Then invoke it:

```bash
/gossip-girl:brief this week in AI agents
```

### Option B – install from this repo (it is its own marketplace)

This repo ships a `.claude-plugin/marketplace.json`, so it doubles as its own marketplace. Add it and install directly:

```bash
/plugin marketplace add dot-naomi/gossip-girl
/plugin install gossip-girl@naomi-plugins
/reload-plugins
```

`naomi-plugins` is the marketplace name; `gossip-girl` is the plugin. Refresh to the latest version anytime with `/plugin marketplace update naomi-plugins`.

### Option C – install from the community catalog

Not yet listed. If this plugin is accepted into Anthropic's community marketplace, it will also install with:

```bash
/plugin marketplace add anthropics/claude-plugins-community
/plugin install gossip-girl@claude-community
/reload-plugins
```

Until then, use Option A or B.

The skill is both slash-invocable (`/gossip-girl:brief`) and model-invocable (Claude reaches for it automatically when a briefing fits the task). It works with or without an argument; with no input it asks for a topic or a paste-in.

Restart any open Claude Code session, or run `/reload-plugins`, after installing. See [TESTING.md](TESTING.md) for a full test plan (core paths, edge-case probes, and how to verify the learning loop).

## Personalization (optional)

The plugin keeps a memory of the themes you keep engaging with, at `skills/brief/references/reader-interests.md`, and weights future briefings toward them. It updates only from clear signals, and records topics only, never personal content.

**Your interests stay on your machine.** The repo ships only `reader-interests.example.md`, an empty cold-start template. On first run the plugin copies it to `reader-interests.md`, where your signals accumulate locally. That working file is git-ignored, so it is never committed or shared. Nothing syncs to other installs or back to the author, and a fresh install starts blank.

How it learns:

- **In-conversation:** ask a follow-up, say "more of this," or click into an item, and that theme's tally goes up.
- **Other channels (opt-in):** ask it to fold in what you forward, reply to, save, or react to (Gmail, Slack, bookmarks). This runs only when you request it, reads only the channels you name, stays read-only, and reports back what it recorded so you can veto it. Connectors must already be authorized; it will not work around auth.

To wipe the memory and start over, delete `reader-interests.md`. The plugin recreates it from the template on the next run.

Sources for triangulation deliberately span primary/official pages, financial press (Yahoo Finance, Bloomberg, TechCrunch, The Information) for money stories, and independent tech-media newsletters (Platformer, Newcomer, Stratechery, Import AI, The Batch, Axios Pro Rata) for framing.

## A note on voice

Built to run well on expressive models, since the register rewards a model that will commit to the conceit. If a run nails the substance but the voice reads flat, the fix is upstream: have the model study `skills/brief/references/voice.md` and the register section of `skills/brief/SKILL.md` before writing, rather than reaching for the voice mid-draft.

## Forking and customizing

The voice and the sourcing rules are both editable in place:

- **Retune the voice:** edit `skills/brief/references/voice.md`, which holds the calibrated example lines the plugin studies before writing. Swap the Gossip Girl register for another and the machinery still works.
- **Change the sourcing:** the triangulation protocol and the named outlet list live in `skills/brief/SKILL.md`.
- **Before redistributing a fork,** confirm `reader-interests.md` is absent or blank so you are not shipping your own interest profile. It is git-ignored by default, so this normally takes care of itself.

## License

MIT. See [LICENSE](LICENSE).
