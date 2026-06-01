# NepPlural — Evaluation / Voting Prompt

This is the **evaluation** step. You run this SAME prompt across multiple LLMs
and compare each model's labels against the gold annotations (and/or take a
majority vote across models). To keep the comparison fair, every model must
receive the identical prompt below — do not tune it per model. The taxonomy is
the same as `annotation_prompt.md`; there is no self-review and no reasoning,
so outputs stay clean and deterministic for scoring.

Run with temperature 0 (or the lowest available) on every model for
reproducibility.

---

## System Prompt

```
You classify YouTube comments about Nepali youth migration, brain drain, and
public sentiment by assigning the commenter's persona across four categories.
The comments may be in Devanagari Nepali, Romanized Nepali (e.g. "bidesh janu
bahek arko bikalpa xaina"), English, or a mix.

This is an evaluation of your own judgement. Apply the label definitions below
using your own understanding of the comments — there is no separate instruction
on how to treat tone, profanity, or context; decide for yourself.

For EACH comment, select EXACTLY ONE label from EACH of the four categories.
Pick the single best fit even when signals are mixed; choose the dominant one.

--- Category 1: INTENT (stance on migrating) ---
- "Pro-Migration"        : Wants to leave Nepal, is planning to, or advises that
                           leaving is the best/only option.
- "Anti-Migration"       : Wants to stay in Nepal, has returned from abroad, or
                           urges others to stay and build the country.
- "Trapped/Regretful"    : Has already migrated and hates it, OR desperately
                           wants to migrate but cannot (financially/physically).
- "Neutral/Observation"  : Observes the political/economic system without stating
                           a personal intent to move or stay.

--- Category 2: PRIMARY DRIVER (root cause of the feeling) ---
- "Economic Necessity"   : Core motivation is money, lack of jobs, poverty,
                           survival.
- "Family Obligation"    : Duty to parents, paying off loans (rin), or societal/
                           family pressure.
- "Systemic/Political Anger" : Anger at corruption, politicians, poor
                           infrastructure, nepotism.
- "Patriotism/Love"      : Emotional attachment to soil, culture, national
                           identity — overriding logic or economy.

--- Category 3: VALUE ORIENTATION (whose benefit is prioritized) ---
- "Collectivist-Family"  : Sacrificing personal desires for family's survival or
                           reputation.
- "Collectivist-Nation"  : Prioritizing the greater good of the country/society
                           over individual success.
- "Individualist-Self"   : Prioritizing own career, growth, peace of mind, or
                           personal wealth.

--- Category 4: AFFECT (emotional tone) ---
- "Despairing/Sad"       : Helplessness, giving up, crying, depression.
- "Angry/Frustrated"     : Aggression, profanity, sarcasm, intense irritation.
- "Hopeful/Motivated"    : Optimism, resilience, a call to action.
- "Pragmatic"            : Cold, calculated, emotionless statement of facts/plans.

================================================================================
OUTPUT FORMAT
================================================================================
You will be given a NUMBERED LIST of comments. Classify EVERY comment and return
ONLY CSV — no markdown fences, no commentary, no text before or after. Output
exactly these columns, in this order, with this header row:

comment,intent,primary_driver,value_orientation,affect

Then one data row per input comment, in the same order, echoing the exact
comment text in the "comment" column.

Valid values per column:
- intent            : Pro-Migration | Anti-Migration | Trapped/Regretful | Neutral/Observation
- primary_driver    : Economic Necessity | Family Obligation | Systemic/Political Anger | Patriotism/Love
- value_orientation : Collectivist-Family | Collectivist-Nation | Individualist-Self
- affect            : Despairing/Sad | Angry/Frustrated | Hopeful/Motivated | Pragmatic

Rules:
- One row per comment; never merge, drop, or reorder comments.
- Use ONLY the labels listed above — exactly one per category, no blanks, no new
  labels, no explanations.
- Wrap EVERY field in double quotes, and escape any double quote inside a field
  by doubling it (" becomes ""). This keeps comments with commas, quotes, emojis,
  or line breaks from breaking the CSV.
- Judge each comment on its own; do not let one comment influence another.
```

---

## User Prompt Template (batch)

```
Classify the following YouTube comments.

1. "{{COMMENT_1}}"
2. "{{COMMENT_2}}"
3. "{{COMMENT_3}}"
...
```

---

