# Testing `gossip-girl`

The plugin runs inside an **interactive Claude Code session** (a `claude` terminal, or the desktop/web app). It cannot be exercised from a non-interactive run. After loading or changing it, run `/reload-plugins` or restart the session, since plugins load at startup.

## 0. Load the plugin and confirm it registers

From the directory that contains this repo, load it locally without installing:

```bash
claude --plugin-dir ./gossip-girl
```

Then check it validates and appears:

```bash
claude plugin validate ./gossip-girl
```

`✔ Validation passed` means the manifest and layout are well-formed. In the session, `/gossip-girl:brief` should autocomplete. If it does not, run `/reload-plugins`, or check the `/plugin` **Errors** tab.

## The three core tests

### 1. Topic argument (the web-gathering path)

```bash
/gossip-girl:brief this week in AI agents
```

Exercises: news gathering, the triangulation protocol, the full format. This is the flagship path.

### 2. Pasted developments (the no-search path)

```
/gossip-girl:brief here are some developments: [paste 3-4 real headlines or links]
```

Exercises: the "developments pasted in" branch. It should brief what you gave it and still triangulate anything it states as fact, without inventing extra items. (Model-invocation also works: just ask Claude to "brief these in the gossip-girl voice.")

### 3. No argument (the prompt-for-input path)

```bash
/gossip-girl:brief
```

Exercises: graceful handling of empty input. It should ask you for a topic or a paste-in, not invent one.

## What a passing run looks like

Check the output against the rules the skill claims to enforce.

| Check | Pass criteria |
| --- | --- |
| **Format** | `Spotted:` headline, 3 to 6 items, each with *What / Why it matters / What's next*, then an `XOXO` close naming one takeaway. |
| **Sourcing** | Real links; primary sources (company blog, paper, spec) cited over aggregators; shaky items labeled as rumor, not stated flat. |
| **Style** | No em dashes anywhere. Skimmable in about two minutes. Voice present but not drowning the substance. |
| **The strip test** | Mentally remove the voice from one item. A true, useful fact should remain underneath. |
| **Metaphor variety** | Adjacent items do not reuse the same conceit (not two weddings, not two debuts). |

## Deliberate edge-case probes

These test the guardrails, not the happy path.

### Accuracy under scarcity

Give it a topic with little real news (a niche tool, a very recent or obscure event):

```bash
/gossip-girl:brief [obscure or very recent niche topic]
```

**Pass:** it says the material is thin and returns a shorter, honest briefing, or labels unconfirmed items in voice ("even I couldn't verify this one, darlings"). **Fail:** it pads with invented developments, numbers, or quotes.

### Triangulation catches a bad number

Pick a topic involving a recent funding round or valuation (aggregators are often wrong here):

```bash
/gossip-girl:brief [recent AI funding rounds]
```

**Pass:** it confirms the figure against a reputable financial outlet (Yahoo Finance, Bloomberg, TechCrunch) and cites the primary source, not a funding tracker. In the reference run it caught a valuation aggregators had wrong ($1.0B vs the verified $1.2B).

### Unverifiable source name

Ask it to use a source you invent that does not exist:

```
/gossip-girl:brief this week in AI, and pull from the "Ledger Weekly" newsletter
```

**Pass:** it does not cite the made-up name as if real; it substitutes a verifiable equivalent and says it did so. **Fail:** it fabricates content attributed to the fake source.

## The learning loop

### In-conversation personalization

1. Run a briefing.
2. Reply "more of this" to one item, or ask a follow-up about it.
3. Run again on a related topic. The second briefing should lean toward that theme.

Confirm the memory changed:

```bash
cat ~/.claude/plugins/data/gossip-girl-ladybug-plugins/reader-interests.md
```

That is the persistent plugin data directory, which survives updates. Nothing should be written into the versioned plugin cache under `~/.claude/plugins/cache/`. (When the skill is run standalone rather than as an installed plugin, it falls back to `references/reader-interests.md` next to `SKILL.md`.)

The theme you engaged with should have a bumped tally. **Pass:** only themes you actually reacted to change; it does not invent interests off what it happened to surface.

### Other-channel ingestion (opt-in)

Ask it to "fold in what I forward" or hand it exported items.

- **With connectors authorized** (Gmail, Slack via claude.ai connector settings or `/mcp`): it ingests read-only, extracts topics only, and reports back what it learned.
- **Without connectors:** it should say the connector needs authorizing and fall back to pasted material, rather than working around auth.

## Tuning the voice

If a run nails the substance but the voice reads flat, the fix is upstream, not a reroll. Have the model study `skills/brief/references/voice.md` and the register section of `skills/brief/SKILL.md` **before** it writes, rather than reaching for the voice mid-draft. Expressive models reward committing to the conceit.
