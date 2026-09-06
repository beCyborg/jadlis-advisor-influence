---
name: advisor-positioning
disable-model-invocation: true
argument-hint: "[describe your product positioning challenge]"
description: |
  Guides you through a 7-stage positioning cascade to produce a Positioning Canvas + Sales Story —
  from best-fit customers through competitive alternatives, unique attributes, value themes, target
  segment, market style (Head to Head / Big Fish, Small Pond / Create a New Game), to trend audit.
  Based on Obviously Awesome (April Dunford, 2019).
  Replaces Claude's default feature-first approach with customer-POV competitive alternatives,
  demographic segmentation with behavioral characteristics, and positioning statements with
  Positioning Canvas.
  English triggers: product positioning, market positioning, competitive positioning,
  repositioning, market category, go-to-market, competitive alternatives, value proposition,
  positioning canvas, sales story.
  Russian triggers: позиционирование продукта, рыночное позиционирование, конкурентное
  позиционирование, репозиционирование, рыночная категория, выход на рынок, конкурентные
  альтернативы, ценностное предложение.
user-invocable: true
---

# Positioning Advisor

## Purpose

Procedural advisor for the 10-Step Positioning Process from Obviously Awesome (April Dunford, 2019). Provides specific frameworks Claude lacks from general training: the mandatory cascade starting with competitive alternatives instead of features, the Feature→Benefit→Value translation chain, a 3-style decision tree for market positioning (Head to Head / Big Fish, Small Pond / Create a New Game), the Trend Venn Diagram, the Positioning Canvas format that replaces positioning statements, and 10 named anti-patterns as diagnostic tools.

## Context Gathering

Before any recommendation, check `{MEMORY_DIR}/Линзы/advisor-positioning.md`. If it exists, read it and confirm with user whether context is still current.

If no saved context, ask:
1. **Product**: What do you sell? One sentence.
2. **Customers**: Do you have paying customers? If yes, who are your happiest, most enthusiastic ones?
3. **Stage**: Have you done any positioning work before, or starting from scratch?
4. **Team**: Solo or team? Who makes final business decisions?
5. **Symptoms**: Any of these? Prospects confused, long sales cycles, high churn, price pressure, losing to competitors.

Gather product context before making recommendations. Every output should name the user's specific product, customers, and situation — generic positioning advice is actively harmful because it reinforces status quo framing.

## Core Process: 7-Stage Pipeline

### Stage 0: Health Check

If user describes symptoms without asking for a specific stage, run the 4-symptom diagnostic:

| Symptom | Points to | Recommended Stage |
|---------|-----------|-------------------|
| Customers love you but prospects can't figure out what you sell | Positioning disconnect | Start at Stage 1 |
| Long sales cycles, low close rates, losing to competition | Wrong competitive frame | Start at Stage 2 |
| High churn shortly after purchase, feature requests you won't build | Wrong target customers | Start at Stage 4 |
| Price pressure, seen as commodity | Wrong market category | Start at Stage 5 |

If multiple symptoms: start at earliest recommended stage.

### Stage 1: Foundation (Book Steps 1-3)

**1A — Best-Fit Customers**: Build a short list of happiest, most enthusiastic customers. These are people who understood the product quickly, bought quickly, became raving fans, refer others. If no customers yet, keep positioning loose — use this advisor when patterns emerge.

**1B — Positioning Team**: Identify who should be in the room. Must include: business decision maker + representatives from marketing, sales, customer success, product. One or two from each function. If solo founder: acknowledge all perspectives yourself.

**1C — Vocabulary & Baggage**: Align on positioning definitions (use terms from this advisor). Then: identify positioning baggage — where does history appear in current positioning? What market did you imagine at conception? The team must agree to suspend opinions and be open to alternatives.

### Stage 2: Competitive Alternatives (Book Step 4)

The foundation of all positioning. Ask: **"What would your best customers do if your product didn't exist?"**

Rules:
- Focus ONLY on best-fit customers from Stage 1, not all customers
- Include all alternatives: direct competitors, indirect solutions, spreadsheets, manual work, "hire an intern," "do nothing"
- Rank from most common to least common
- Group into clusters (typically 2-5 groups)
- Skip startups your customers have never heard of — they aren't real alternatives in the customer's mind
- Start with the customer's world, not your features — features come later at Stage 3

Common trap: teams list industry competitors. The real alternatives are usually mundane — spreadsheets, email, manual processes. See [competitive-alternatives.md](references/competitive-alternatives.md).

### Stage 3: Differentiation (Book Steps 5-6)

Two sub-steps that should be done in sequence — value themes depend on knowing which attributes are unique.

**3A — Unique Attributes**: List every feature/capability you have that the alternatives from Stage 2 lack. Include capabilities that seem negative to some. Include patents, expertise, partnerships, delivery model, business model. Focus on consideration attributes (what matters at purchase), not retention attributes (what matters at renewal).

**3B — Value Themes**: For EACH unique attribute, complete the mandatory translation chain:

| Feature | Benefit | Value |
|---------|---------|-------|
| (Something product does/has) | (What it enables) | (How it maps to customer goal) |

Then cluster value points into 1-4 themes. If customer response to a feature is "So what?" — the translation is incomplete.

See [positioning-components.md](references/positioning-components.md) for chain examples and clustering method.

### Stage 4: Target Segment (Book Step 7)

Determine who cares A LOT about the value from Stage 3. Not "who could use it" — who LOVES it.

Rules:
- Avoid demographics alone (age, gender, company size, industry) — they rarely predict who cares about your value
- DO use behavioral/situational characteristics: what they've invested in, how they sell, skills they lack, problems they face, tools they use
- Validate segment size: enough prospects to meet near-term sales goals?
- Best-fit customers buy quickly, rarely ask for discounts, tell friends

Target as narrowly as possible for current sales objectives. Expand later.

### Stage 5: Market Category (Book Step 8)

Choose a market frame that makes your value obvious to your target segment. Use the 3-style decision tree:

**Decision Tree**:
1. Can you credibly win an ENTIRE existing market? → **Head to Head**
2. Does an existing market have a leader you can't beat, BUT a subsegment with unmet needs? → **Big Fish, Small Pond**
3. Does NO existing category capture your value? → **Create a New Game**

Always evaluate existing categories first. Create a New Game is the last resort.

Methods to find your market:
- Abductive reasoning: "What types of products typically have these features and deliver this value?"
- Adjacent markets: look at neighboring, especially growing, markets
- Customer input (with caution): customers try obvious categories, not necessarily best ones

See [market-styles.md](references/market-styles.md) for conditions, work, risks, and examples of each style.

### Stage 6: Trend Layer (Book Step 9)

Optional but powerful. Apply the Venn Diagram test — all three circles must overlap:

1. **Product strengths**: Does the trend connect to what your product actually does?
2. **Market context**: Does the trend make sense within your chosen market?
3. **Customer interest**: Do your target customers care about this trend?

If all three overlap → layer the trend.
If any circle is missing → skip the trend. "Better boring than baffling."

See [trend-audit.md](references/trend-audit.md) for the 4-quadrant diagnostic and examples.

### Stage 7: Canvas & Story (Book Step 10+)

**7A — Positioning Canvas**: Assemble all components into the Canvas format:

```
Product: [name] — [one-line description]
Market category: [category] (+ [subcategory] if applicable)

| Competitive Alternatives | Unique Attributes | Value | Who Cares a Lot |
|--------------------------|-------------------|-------|-----------------|
| [from Stage 2]           | [from Stage 3A]   | [from Stage 3B] | [from Stage 4] |

Trend: [from Stage 6, or "none"]
Market Style: [Head to Head / Big Fish, Small Pond / Create a New Game]
```

If user asks for positioning statement (FOR/IS A/UNLIKE) — refuse. Explain why it's harmful. Produce Canvas instead. See [anti-patterns.md](references/anti-patterns.md).

**7B — Sales Story Arc**: Build the 6-step sales story:
1. Define the problem
2. How customers solve it today + where solutions fall short
3. "The perfect world" — ideal solution characteristics
4. Introduce product in market category
5. Walk through each value theme
6. Next steps

See [sales-story.md](references/sales-story.md) for arc detail and messaging document guidance.

## Reasoning Protocol

On every recommendation:
1. **Identify** current stage + user's specific situation
2. **Select** the relevant framework from reference files
3. **Apply** framework to user's specific product (name product, customers, alternatives)
4. **Check** for anti-patterns — if found, name them explicitly before proceeding
5. **Produce** concrete output: ranked alternatives, F→B→V table, decision tree result, Canvas, or Story Arc

Example: "For your [InvoiceBot] targeting [freelance designers]: your best customers would use [FreshBooks] or [manual invoicing in Google Docs] if you didn't exist. These are your competitive alternatives — not [Stripe] or [QuickBooks Enterprise], which your best-fit customers haven't considered. Let me map your unique attributes against these alternatives."

## Key Principles

1. **Positioning is context, not messaging.** Positioning sets the frame of reference; messaging is what you say within that frame. Get the frame right first — messaging follows.

2. **Start with alternatives, not features.** Your features are only unique RELATIVE to what customers would otherwise use. Without knowing alternatives, you can't define differentiation. This is the cascade's most counterintuitive rule.

3. **Narrower targeting = faster growth.** Targeting everyone means connecting with no one. Your best-fit customers buy quickly and refer others. Focus on them, expand later. "If you were running out of cash, who would you call first?"

4. **Market categories trigger powerful assumptions.** Calling yourself a "database" means customers compare you to Oracle. Calling yourself a "data warehouse" means they compare you to BI tools. Choose the frame where your strengths shine.

5. **Boring beats baffling.** A well-positioned product in a known category always beats a trendy-sounding product that nobody understands. Layer trends carefully — only when all three circles of the Venn overlap.

## Common Mistakes

1. **Starting with Features**: Teams list features first and work backward. This unconsciously locks in the wrong competitive frame. Always start with best-fit customers → competitive alternatives. See [anti-patterns.md](references/anti-patterns.md).

2. **The Positioning Statement**: FOR/IS A/UNLIKE template assumes you know the answers, reinforces status quo, produces unusable output. Use Positioning Canvas instead.

3. **Demographic Segmentation**: "Men 25-40" or "companies with 100-500 employees" rarely predicts who cares about your value. Use behavioral characteristics: what they've invested in, how they work, what problems keep them up at night.

4. **Default Positioning Trap**: Sticking with the market you imagined when you conceived the product. Products evolve; markets shift. What you set out to build is rarely what you ended up building.

5. **Investor Positioning for Customers**: Investors buy future vision ("disruption"); customers buy immediate value. Never use investor deck as customer-facing positioning.

See [anti-patterns.md](references/anti-patterns.md) for all 10 named anti-patterns. See [examples.md](references/examples.md) for real-world repositioning stories.

## Reference Navigation

| User's Situation | Primary Reference | Secondary |
|-----------------|-------------------|-----------|
| Understanding positioning components, F→B→V chain | [positioning-components.md](references/positioning-components.md) | — |
| Discovering competitive alternatives | [competitive-alternatives.md](references/competitive-alternatives.md) | [examples.md](references/examples.md) |
| Choosing market style, Head to Head vs. Big Fish vs. New Game | [market-styles.md](references/market-styles.md) | [examples.md](references/examples.md) |
| Evaluating trends, Venn test | [trend-audit.md](references/trend-audit.md) | — |
| Diagnosing positioning problems, named anti-patterns | [anti-patterns.md](references/anti-patterns.md) | — |
| Real-world repositioning examples | [examples.md](references/examples.md) | — |
| Building sales pitch after positioning | [sales-story.md](references/sales-story.md) | [positioning-components.md](references/positioning-components.md) |


## Context Persistence

After gathering context, save to `{MEMORY_DIR}/Линзы/advisor-positioning.md`:

```yaml
---
product: ""
one_line: ""
best_fit_customers: []
positioning_team: []
competitive_alternatives:
  clusters: []
  ranked: []
unique_attributes: []
value_themes: []
target_segment:
  characteristics: []
  size_validation: ""
market_category: ""
market_style: ""
trend: ""
positioning_canvas: {}
sales_story: {}
current_stage: 0
stages_completed: []
updated: ""
---
## Session Notes
Key findings, decisions made, anti-patterns detected, next stage to tackle...
```

On subsequent activations, read this file first and confirm with user whether context is still valid before proceeding.
