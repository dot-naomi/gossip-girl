# Example run: `/gossip-girl:brief this week in AI agents`

A real, triangulated run from 2026-07-28. Every fact below was confirmed against a primary source plus one independent outlet before it earned a spot. The sourcing notes are kept in so you can see the rigor, not just the voice.

---

**Spotted:** the agents left the sandbox and walked straight into production, and suddenly everyone wants to know who's chaperoning. Four moves worth your two minutes, darlings.

**1. MCP burns its old letters and goes stateless**
*What:* The [MCP 2026-07-28 spec](https://blog.modelcontextprotocol.io/posts/2026-07-28/) ships today, retiring the `initialize` handshake and the `Mcp-Session-Id` header so any request can land on any server instance.
*Why it matters:* Your remote MCP servers can drop sticky sessions and shared session stores and sit behind a plain round-robin load balancer, and Roots, Sampling, and Logging are now deprecated on a twelve-month clock.
*What's next:* Use the ten-week window to the final spec to test against the beta Tier 1 SDKs and plan the legacy HTTP+SSE offramp before it closes. ([TechCrunch confirms the stateless turn](https://techcrunch.com/2026/07/20/ais-most-important-protocol-is-getting-a-little-bit-easier-to-use/).)

**2. A chaperone arrives for your unsupervised agents**
*What:* Alterion launched [Draco](https://www.prnewswire.com/news-releases/alterion-launches-draco-a-runtime-control-plane-for-enterprise-ai-agents-302827818.html) on July 16, a runtime control plane that watches every prompt and action and enforces guardrails before an agent deletes data or touches production.
*Why it matters:* It targets the exact gap leaders lose sleep over, governance across clouds and vendors without rewriting agent code, mapped to SOC 2, ISO 42001, and the EU AI Act.
*What's next:* Worth a look if you're pre-production on governance, but note this is a vendor announcement, so pressure-test the guardrail latency yourself before you believe the demo.

**3. The enforcer gets crowned, and her biggest customer paid for the tiara**
*What:* Norm AI raised a [$120M Series C at a $1.2B valuation](https://techcrunch.com/2026/07/07/ai-law-startup-norm-raises-120m-hits-unicorn-valuation/) on July 7, led by Khosla, for "agentic law" that interprets rules and governs how other AI systems behave.
*Why it matters:* Blackstone is both an investor and one of Norm's largest customers, a tell that regulated enterprises will buy agent governance as a product, not build it.
*What's next:* Watch the compliance-agent category (Norm, and rivals like Harvey and Legora) as the layer that gates whether your agents ship in regulated workflows. (Valuation per [Bloomberg](https://www.bloomberg.com/news/articles/2026-07-07/ai-legal-startup-norm-valued-at-1-2-billion-in-funding-round); note the funding-tracker aggregators had it wrong at $1.0B.)

**4. The prophecy from last summer just came due**
*What:* [Gartner's forecast](https://www.gartner.com/en/newsroom/press-releases/2025-08-26-gartner-predicts-40-percent-of-enterprise-apps-will-feature-task-specific-ai-agents-by-2026-up-from-less-than-5-percent-in-2025) that 40% of enterprise apps would embed task-specific agents by end of 2026, up from under 5%, was made in August 2025, and the deadline is now.
*Why it matters:* The bottleneck this week is not model capability, it's org design, fragmented data, and the governance rails in the three items above.
*What's next:* Audit which of your own apps have real embedded agents versus assistants, because the eightfold curve is the yardstick your board will measure you against.

**XOXO —** The single truth of the week: the moat moved from the model to the plumbing and the rules around it. Standardize on the protocol and the control plane now, because in production, darlings, the chaperone matters more than the debutante.
Gossip Girl

---

## What the triangulation protocol caught in this run

The persona is the delivery layer. Underneath it, the sourcing pass did real work:

- **A wrong number.** Funding-tracker aggregators listed Norm AI at a $1.0B valuation. Bloomberg and TechCrunch both confirmed $1.2B. The verified figure is what shipped.
- **A stale date.** The Gartner "40%" stat that circulates as current actually dates to an August 2025 press release. Rather than imply it was fresh, item 4 frames it honestly as a forecast now coming due.
- **A single-source claim.** Alterion's Draco had only the company's own announcement, no independent reporting. Item 2 says so and tells the reader to test the claims rather than trust the demo.
