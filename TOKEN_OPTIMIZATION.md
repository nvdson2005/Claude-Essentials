# Token Optimization

## Before the Session Starts
### Write a CLAUDE.md (and/or AGENTS.md) file

This is Claude Code’s native mechanism for persistent project context — it gets read at session start and doesn’t need to be re-injected manually.

Can use Claude to generate this automatically.

### Pre-scope your task before starting

Before starting to go into tasks, write out your task spec before starting the session. Know exactly:

* What file(s) are involved
* What the end state should look like
* What constraints apply

### Divide conversations

Each task should be divided into separate conversations. Claude has to re-read the history every time a new request is sent. Dividing into separate conversations allows Claude to focus fully on one context.

### Strip files before including them

If you need Claude to review or modify a file, don’t paste the whole thing if you can avoid it. Remove:

* Comment blocks that don’t affect logic
* Unused imports
* Dead code sections unrelated to the task
* Boilerplate that Claude doesn’t need to see

A trimmed 80-line version of a file uses a fraction of the tokens a 400-line version would, and Claude’s output quality often doesn’t change.

### Break large tasks into session-sized chunks

Some tasks are just too big for one session. Trying to cram them in leads to rushed, truncated outputs as the context fills.

Before starting, break the task into discrete units that can each be completed in one focused session:

“Refactor user model” → separate from “Refactor auth routes” → separate from “Update API tests”
This also makes it easier to pick up where you left off if a session ends early.

### Use a task brief template

Develop a personal template for how you open sessions. Something like:

```
Context: [2-3 sentences on what the project is]
Goal: [1 sentence on what this session accomplishes]
Files: [List of relevant files only]
Constraints: [Any important rules or patterns to follow]
Task: [Specific thing to do]
```

Structured prompts get more accurate responses in fewer turns — which directly saves tokens.

### Pre-generate a project map

Use a tool like tree or a custom script to generate a compact directory structure of your project, then paste only the relevant portion when starting a session. This gives Claude spatial context without requiring it to explore the codebase blindly, which would cost additional tokens in file read operations.

### Use `.claudeignore` to exclude irrelevant files

If your project has large files or directories that aren’t relevant to the task at hand, use a `.claudeignore` file to exclude them from Claude’s context. This prevents Claude from wasting tokens on reading and processing files that don’t contribute to the current session’s goals.

## During the Session

### Write a CLAUDE.md file

Rewrite the CLAUDE.md file with the new context after each session. This way, when you start the next session, Claude can read the updated CLAUDE.md and have all the necessary context without needing to re-inject it manually.

### Suppress unnecessary explanations

By default, Claude explains its reasoning in detail. That’s helpful when you’re learning — it’s expensive when you’re shipping.

Add to your prompts:

* “No explanations, just the code.”
* “Skip the preamble, output only the implementation.”
* “Don’t recap what we discussed, just proceed.”

This alone can cut Claude’s response tokens by 30–50% on coding tasks.

### Use the `/compact` command strategically

Claude Code includes a `/compact` command that summarizes the conversation history and replaces it with a compressed version. This frees up significant context window space.

Use it proactively — don’t wait until you’re near the limit. Compact after completing a discrete sub-task, before moving to the next one. Think of it as checkpointing.

The summary loses some granular detail, so compact at natural breakpoints where the completed work is already solid.

### Give Claude a “memory anchor” when compacting

Before you run `/compact,` explicitly ask Claude to preserve key decisions in its summary:

```“Before we compact, note that we decided to use optimistic locking for the user update flow, and we’re avoiding any changes to the schema in this session.”```

This shapes what the compaction summary captures and prevents critical context from getting lost in the compression.

### Avoid asking Claude to repeat or reformat what it just wrote

A common token sink: “Can you also give me that as a bulleted list?” or “Rewrite that more concisely.”

If you need something reformatted, do it yourself or ask Claude to output it in the format you need the first time. Re-requesting means Claude regenerates content it already produced, doubling the token cost.

### Keep your messages short and directive

Long user messages use tokens too. Don’t write paragraphs when a sentence will do. Claude doesn’t need:

Extended background context it already has
Diplomatic softening language (“I was hoping you might be able to…”)
Repeated acknowledgments (“Great, thanks for that, now can you…”)

## Using Plugins and Tools

### Be selective with tool use
Tools and plugins can be powerful, but they also consume tokens. Only use them when they provide a clear benefit that outweighs the token cost. For example, if you have a plugin that generates test cases, use it for complex logic but write simple tests yourself.

### Optimize shell command usage
If you’re using a plugin that executes shell commands, be mindful of how many commands you’re asking Claude to generate. Instead of asking for a full script, ask for a high-level outline and then fill in the details yourself. This reduces the number of tokens spent on command generation and allows you to optimize the commands for your specific environment.

### Use caching for repeated information
If there’s information that Claude needs to reference multiple times (like a specific API endpoint or a particular code snippet), consider caching that information in a separate file or document. Then, when Claude needs to reference it, you can simply point it to the cached version instead of having it regenerate the information each time.

### Usable tools that optimize token usage:
Resources for token optimization:
* https://github.com/JuliusBrussee/caveman: Shorten agents response to reduce tokens in output.
* https://github.com/rtk-ai/rtk: Reduce the number of shell commands agents consume.
