# agentic-coding

**Priority**: high
**Type**: permanent
**Max sparks per scan**: 30

Concrete, reproducible techniques for shipping full-stack features with AI agents. Not hype, not "I 10x'd my productivity" without receipts. I want the actual workflows, configs, hooks, and scripts that experienced practitioners use — with links to repos or demos where possible.

## Where I'm at (so the radar knows what's novel vs. known)
- Frontier models + context engineering (understand well, actively practice)
- Deterministic gates via Claude Code hooks (e2e tests as pre-commit/post-tool hooks)
- OODA/Ralph loops (aware of concept, haven't deeply implemented)
- Single-agent workflow, clearing sessions between features
- Heavy Wispr Flow usage — 20-30 min voice dumps for feature specs
- Design iteration: ask agent for 15 variations, give 20 min voice feedback, converge on final
- Pain point: big full-stack features often end in waste and regressions
- Pain point: no great process for upfront design before implementation
- Pain point: unclear when to break work into multiple sessions vs. push through

## Key questions
- What specific agentic coding setups (hooks, skills, scripts, loops) are top practitioners actually using? Show me the repos, the configs, the CLIs — not the vibes.
- How do engineers who ship large features avoid the regression/waste spiral? What gates, checkpoints, or decomposition strategies actually work?
- What multi-agent or orchestrated workflows are people using in practice (not demos)? Subagent patterns, parallel execution, plan-then-execute — what's proven?
- How are people doing upfront design with agents before writing code? Spec generation, architecture review, mock iteration — what's the workflow that avoids "just start coding and hope"?
- What's the state of the art for context management across long feature implementations? Session strategies, memory, plan files, CLAUDE.md patterns that keep the agent on track.
- What's the state of the art for deep AI code review? Especially: Anthropic's Claude code review GitHub Action (closed-source, API-only) — can it be run locally with a Claude subscription? What are the deepest code review and code simplification flows/commands people are using?
- What concrete experiments should I try next given my current setup? Specific techniques with expected outcomes, not vague advice.

## What I don't want
- Vague productivity claims without methodology ("I ship 2000 LOC/hour")
- Tool announcements without adoption evidence
- Theoretical frameworks without production validation
- Anything I already know (see "Where I'm at" above) unless there's a genuinely new angle

## Source preferences
GitHub repos with actual configs/hooks/skills > production write-ups with specifics > conference talks with demos > blog posts with code > everything else

## Trusted voices
(to be refined via feedback)
