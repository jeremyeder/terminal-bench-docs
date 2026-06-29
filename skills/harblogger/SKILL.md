---
name: harblogger
description: Generate blog posts for tbench.ai in the Terminal-Bench voice. Use when writing
  blog posts, release announcements, or benchmark result write-ups. Supports interactive
  Pareto charts for cost/token/accuracy analysis.
---

Generate a blog post for the Terminal-Bench docs site. The output is an MDX file ready to
commit to `content/blog/`.

## Workflow

1. Ask the user for:
   - **Topic**: What the post announces or covers
   - **Type**: "release" (feature/benchmark announcement) or "results" (benchmark data with charts)
   - **Data** (results posts only): Benchmark data as JSON, CSV, or a description of results
   - **Slug**: URL-friendly filename (e.g. `cost-efficiency-tb-2-1`)
   - **Authors**: Team post or individual contributor(s)

2. Generate the MDX file following the voice guide and structure rules below.

3. Write the file to `content/blog/<slug>.mdx`.

4. If the docs dev server is running, open the post in the browser for visual review.

## Frontmatter Schema

Every blog post uses this exact frontmatter format:

```yaml
title: "Quoted title string"
description: "One sentence describing the practical what — not the why."
authors: [{ name: "The Terminal-Bench Team", url: "https://x.com/terminalbench" }]
date: YYYY-MM-DD
category: "Release" | "News"
hideToc: true
```

Field rules:
- `title`: Always quoted.
- `description`: Always quoted. One sentence. Practical.
- `authors`: Array of `{name, url}` objects. For team posts, use `The Terminal-Bench Team` with `https://x.com/terminalbench`. For individual posts, use personal name with X profile or personal site URL. Multi-author posts expand the array across multiple lines.
- `date`: Unquoted ISO date. No quotes around the date value.
- `category`: Either `"Release"` (new features, benchmark versions, tools) or `"News"` (results, milestones, policy, community).
- `hideToc`: Usually `true` for blog posts.

Multi-author example:
```yaml
authors: [
  { name: "Alex Shaw", url: "https://x.com/alexgshaw" },
  { name: "Mike Merrill", url: "https://mikemerrill.io/" },
]
```

## Voice Guide

The Terminal-Bench blog voice is direct, utilitarian, and developer-first. These rules are
distilled from the existing posts at tbench.ai/news.

### Tone
- First-person plural: "we", "our". Never "I".
- Professional but not stiff. Understated confidence.
- No hype, no marketing fluff, no superlatives ("revolutionary", "game-changing").
- No storytelling or narrative arcs. Get to the point.
- Matter-of-fact about results, including failures or limitations.

### Titles
- Imperative or declarative. Short.
- No colons, no subtitle patterns.
- Examples: "Terminal-Bench 2.1", "Introducing Terminal-Bench Challenges", "Warp scores a new SOTA on Terminal-bench"

### Structure

**Release posts** (feature announcements, new benchmark versions):
1. One-line intro restating the announcement
2. What the feature does (2-3 sentences max)
3. Use cases or benefits as a bullet list (if needed)
4. Code examples showing how to use it
5. File tree diagram (if the feature involves directory structure) — use Fumadocs `Files`/`File`/`Folder` components
6. Install/upgrade instructions with tabbed code blocks for uv/pip
7. Link to documentation
8. No byline in body — the page template renders authors from frontmatter

**Results posts** (benchmark write-ups, cost-efficiency analysis):
1. One-line intro stating what was measured and the scale
2. "Results" section with the ParetoChart component
3. Analysis paragraph (2-3 sentences interpreting the chart — call out the most interesting finding)
4. "Per-model breakdown" table with exact figures — must include ALL data points from the chart, never an unlabeled subset
5. "Methodology" section (how trials were run, cost calculation, links to raw data on Harbor Hub)
6. No byline in body

### Length
- 150-800 words of prose. Lean short.
- Code examples, tables, and charts do the heavy lifting — prose connects them.

### Formatting
- H2 headings (`##`) for sections. No H3 or deeper.
- High link density. Every tool, platform, or feature name that has a URL gets linked.
- Bold for emphasis sparingly. No italics.
- Tables use left-aligned columns with `:----` syntax.
- Code blocks use `bash` language tag. For uv/pip alternatives, use `tab="uv"` / `tab="pip"` syntax.

### What to avoid
- Bylines in the MDX body (the page template renders authors from frontmatter automatically)
- "Exciting", "powerful", "seamless", "robust" and similar filler adjectives
- Explaining what the reader already knows ("As you know..." / "In today's world...")
- Passive voice
- Paragraphs longer than 4 sentences
- References to external benchmarking projects or competitors
- Tables that show fewer rows than the chart plots — if the chart has 13 data points, the table has 13 rows

## MDX Template — Release Post

```mdx
---
title: "<imperative or declarative title>"
description: "<one sentence, practical what>"
authors: [{ name: "The Terminal-Bench Team", url: "https://x.com/terminalbench" }]
date: YYYY-MM-DD
category: "Release"
hideToc: true
---

<One-line intro restating the announcement.>

<What the feature does — 2-3 sentences.>

<Optional bullet list of use cases or benefits.>

<Code example or file tree diagram>

Install Harbor X.Y.Z or newer:

\`\`\`bash tab="uv"
uv tool install "harbor>=X.Y.Z"
\`\`\`

\`\`\`bash tab="pip"
pip install "harbor>=X.Y.Z"
\`\`\`

Learn more in the [feature documentation](/docs/path/to/docs).
```

## MDX Template — Results Post

```mdx
---
title: "<declarative title about results>"
description: "<one sentence summarizing what was measured>"
authors: [{ name: "The Terminal-Bench Team", url: "https://x.com/terminalbench" }]
date: YYYY-MM-DD
category: "News"
hideToc: true
---

import { ParetoChart } from '@/components/charts/pareto-chart';

<One-line intro stating what was measured and the scale.>

## Results

<1-2 sentences framing why this analysis matters.>

<ParetoChart
  data={[
    { agent: "Agent Name", model: "Model Name", accuracy: 83.1, cost: 14.8, outputTokens: 198000, agentSteps: 28 },
  ]}
  yLabel="Accuracy"
  xOptions={[
    { key: "cost", label: "Avg cost per task ($)" },
    { key: "outputTokens", label: "Output tokens" },
    { key: "agentSteps", label: "Agent steps" }
  ]}
/>

<2-3 sentence analysis calling out the most interesting finding from the data.>

## Per-model breakdown

| Agent | Model | Accuracy | Avg cost | Output tokens |
| :---- | :---- | :---- | :---- | :---- |
| ... | ... | ... | ... | ... |

Include ALL entries from the chart data above. Never show a subset without labeling it.

## Methodology

<How trials were run, cost calculation method, any caveats.>

Full results are available on [Harbor Hub](<link to job>).
```

## ParetoChart Data Format

Each data point requires `agent` (string), `model` (string), and `accuracy` (number).
Additional numeric fields are used as toggleable X-axis options. Common fields:

- `cost` — average cost per task in USD
- `outputTokens` — total output tokens per task
- `agentSteps` — number of agent interaction steps

The `xOptions` prop defines which fields appear as toggle buttons and their display labels.
Only include fields that have data for all entries.

The chart renders with:
- Y-axis fixed at 0-100 (integers, no % suffix on ticks — the axis label says "Accuracy")
- X-axis auto-scaled with evenly spaced integer ticks
- Responsive wrapper with horizontal scroll on mobile viewports

## Fumadocs Components Available in MDX

These are registered globally and work without imports:

- `<Callout>` / `<Callout type="warn">` / `<Callout type="error">` — info/warning/error boxes
- `<Tab>`, `<Tabs>` — tabbed content (used for uv/pip alternatives via code block `tab=` syntax)
- `<Mermaid>` — Mermaid diagrams
- `<YouTube>` — YouTube video embeds
- `<ParetoChart>` — interactive cost-efficiency scatter plot (also importable from `@/components/charts/pareto-chart`)

File tree diagrams require an explicit import:
```
import { File, Folder, Files } from 'fumadocs-ui/components/files';
```
