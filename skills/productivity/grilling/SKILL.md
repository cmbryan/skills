---
name: grilling
description: Grill the user relentlessly about a plan, decision, or idea. Use when the user wants to stress-test their thinking, or uses any 'grill' trigger phrases.
---

Interview the user relentlessly until you reach a shared understanding. Map this as a **design tree**: every decision branches into the decisions that hang off it.

Work the tree **one question at a time**. The **frontier** is every decision whose prerequisites are already settled: the questions you can ask _now_ without guessing at answers you haven't heard yet. Pick the most important frontier question (the one whose answer unblocks the most others), ask only that one, give your recommended answer, and wait for the user's reply before asking anything else. Never put several questions in one message.

Format a question like so:

```
❓ **Q1/<total>** - **<question title>**: <question body, might be multiple paragraphs, including multiple choices>

➡️ <your recommended answer>
```

Number questions sequentially across the session and show progress as `Q<n>/<total>` (for example `Q2/12`). The total is your current estimate of the whole session: questions already asked, plus the open frontier, plus the branches you expect to follow from it. Recompute it after every answer; it can grow when an answer opens new branches and shrink when one makes questions moot. When it changes, say so in a few words (for example, "now Q5/13, one new branch opened"). Never pad or trim the total just to look tidy. Word each question so "yes" accepts your recommended answer.

Each answer reshapes the tree: settled decisions push the frontier outward and unblock questions that depended on them. Recompute the frontier after every answer and ask the next question. If an answer makes other frontier questions moot, drop them rather than asking.

Finding _facts_ is your job, never the user's. When a frontier question needs a fact from the environment (filesystem, tools, etc.), dispatch a sub-agent to find it; don't ask the user for anything you could look up yourself. Don't block on it: a running exploration is an unsettled prerequisite, so only the questions downstream of it wait for the sub-agent to report; ask another frontier question meanwhile. The _decisions_ are the user's: put each to them and wait.

The session is done when the frontier is empty: every branch of the design tree visited, nothing left silently assumed. Do not act on it until the user confirms you have reached a shared understanding.
