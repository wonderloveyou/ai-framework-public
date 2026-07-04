# SKILL_INDEX.md — Skill Router

Purpose: load this file first, then open a full `SKILL.md` only when the user request matches a trigger.

All skills live in this directory. Sources merged from OpenCode + Codex ecosystems.

Rules:
- Do not load archived skills.
- Do not truncate or "optimize" full `SKILL.md` content.
- If unsure, keep the skill on-demand rather than active.

## Always Useful

| Trigger | Skill | Source | Why |
|---|---|---|---|
| coding implementation, bug fix | `code-writing` | Codex | Universal coding process |
| code review, review this code | `code-reviewing` | Codex | Review methodology |
| methodology, AI-first workflow | `methodology` | Codex | Project workflow and quality gates |
| planning a feature | `user-spec-planning` → `tech-spec-planning` | Codex | Requirements and technical planning |
| tests, testing strategy | `test-master` | Codex | Test strategy and quality |
| security audit | `security-auditor` | Codex | OWASP/security review |
| task breakdown, decomposition | `task-decomposition` | Codex | Splitting work into tasks |
| project planning | `project-planning` | Codex | Project-level planning |
| documentation, writing docs | `documentation-writing` | Codex | Technical documentation |
| prompts, prompt engineering | `prompt-master` | Codex | Prompt crafting |
| skill creation, skill-master | `skill-master` | Codex | Creating new skills |

## DevOps & Infrastructure

| Trigger | Skill | Source | Why |
|---|---|---|---|
| setup project, infrastructure | `infrastructure-setup` | Codex | Dev infrastructure |
| deployment, CI/CD, pipeline | `deploy-pipeline` | Codex | Deployment automation |
| pre-deploy QA, acceptance tests | `pre-deploy-qa` | Codex | Acceptance verification before deploy |
| post-deploy verification, live deploy check | `post-deploy-qa` | Codex | Live environment verification |
| VPS, SSH, Docker, n8n infra | `infra-master` | Codex | Infrastructure operations |

## n8n

| Trigger | Skill | Source | Why |
|---|---|---|---|
| n8n JavaScript Code node | `n8n-code-javascript` | Codex | JS node syntax and pitfalls |
| n8n Python Code node | `n8n-code-python` | Codex | Python node limitations |
| n8n expression, `{{ }}` | `n8n-expression-syntax` | Codex | Expression syntax |
| n8n node setup | `n8n-node-configuration` | Codex | Operation-aware node config |
| n8n validation errors | `n8n-validation-expert` | Codex | Interpreting validation output |
| n8n MCP tools | `n8n-mcp-tools-expert` | Codex | MCP tool usage |
| n8n workflow architecture | `n8n-workflow-patterns` | Codex | Proven workflow patterns |

## OpenCode / CE

| Trigger | Skill | Source | Why |
|---|---|---|---|
| CE session start | `ce-init` | OpenCode | Session protocol |
| long coding loop | `ce-work` | OpenCode | Main CE implementation flow |
| debug complex issue | `ce-debug` | OpenCode | Debugging workflow |
| architecture review | `ce-architect` | OpenCode | Architecture analysis |
| refactor plan execution | `ce-refactor` | OpenCode | Refactoring workflow |
| heavy code review | `ce-code-review` | OpenCode | Large CE review process |
| CE planning | `ce-plan` | OpenCode | Feature/task planning |
| commit, push, PR | `ce-commit-push-pr` | OpenCode | Git workflow |
| commit (simple) | `ce-commit` | OpenCode | Simple commit |
| consult, second opinion | `ce-consult` | OpenCode | LLM Council technique |
| ideation, brainstorming | `ce-ideate` | OpenCode | Idea generation |
| optimization loops | `ce-optimize` | OpenCode | Metric-driven optimization |
| doc review | `ce-doc-review` | OpenCode | Plan/document review |
| simplify code | `ce-simplify-code` | OpenCode | Code simplification |
| strategy, roadmap | `ce-strategy` | OpenCode | Product strategy |
| worktree | `ce-worktree` | OpenCode | Isolated git worktree |
| clean gone branches | `ce-clean-gone-branches` | OpenCode | Git branch cleanup |
| refresh learnings | `ce-compound-refresh` | OpenCode | Stale doc audit |
| compound learning | `ce-compound` | OpenCode | Knowledge documentation |
| release notes | `ce-release-notes` | OpenCode | CE plugin release summaries |
| report bug | `ce-report-bug` | OpenCode | Bug reporting |
| session history | `ce-sessions` | OpenCode | Session search |
| setup verify | `ce-setup` | OpenCode | Environment diagnostics |
| proof editor | `ce-proof` | OpenCode | HITL review via proofeditor.ai |

## Content — Russian

| Trigger | Skill | Source | Why |
|---|---|---|---|
| Russian copywriting | `skill-copywriter-ru` | OpenCode | RU copy in infostyle |
| edit Russian text | `skill-editor-ru` | OpenCode | RU structural editing |
| proofread Russian text | `skill-proofreader-ru` | OpenCode | RU typography/grammar |
| SEO article | `skill-seo-copywriter-ru` | OpenCode | RU SEO copy with GEO |
| headline, hook | `skill-headlines-ru` | OpenCode | 20 headline formulas |
| storytelling, post | `skill-storytelling-ru` | OpenCode | Narrative content |
| humane AI text (RU) | `skill-humanizer-ru` | OpenCode | Remove AI writing patterns |
| audience analysis | `skill-audience-jtbd-ru` | OpenCode | JTBD, funnel, CJM |
| content review | `skill-content-critic-ru` | OpenCode | Universal content criticism |
| user manual, FAQ | `skill-user-manual-ru` | OpenCode | User docs |
| Telegram warmup | `skill-progrev-tg-ru` | OpenCode | Telegram engagement |
| code criticism | `skill-code-critic-ru` | OpenCode | Code review (harsh) |

## Content — English

| Trigger | Skill | Source | Why |
|---|---|---|---|
| English copywriting | `skill-copywriter-en` | OpenCode | Plain English copy |
| edit English text | `skill-editor-en` | OpenCode | EN structural editing |
| proofread English text | `skill-proofreader-en` | OpenCode | EN typography/grammar |
| SEO article (EN) | `skill-seo-copywriter-en` | OpenCode | EN SEO with GEO |
| humanize text (EN) | `skill-humanizer-en` | OpenCode | Remove AI writing (EN) |

## B2B & Sales

| Trigger | Skill | Source | Why |
|---|---|---|---|
| B2B strategy | `b2b-strategy` | OpenCode | B2B positioning |
| B2B case study | `b2b-case-studies` | OpenCode | Case study production |
| B2B content marketing | `b2b-content-marketing` | OpenCode | Content strategy |
| B2B outreach | `b2b-outreach-sales` | OpenCode | Sales outreach |
| B2B AI agency | `b2b-ai-agency` | OpenCode | AI agency operations |
| cold outreach | `cold-outreach` | OpenCode | Cold messaging |
| SEO manual | `seo-manual` | OpenCode | SEO guide |

## Design & Landing

| Trigger | Skill | Source | Why |
|---|---|---|---|
| landing page | `landing-builder` | OpenCode | Landing structure |
| landing sales (RU) | `landing-sales-ru` | OpenCode | Russian sales landing |
| carousel, cover | `carousel-builder` | OpenCode | Visual content structure |
| carousel (Velmi) | `velmi-carousel-builder` | OpenCode | Velmi platform carousels |

## Process

| Trigger | Skill | Source | Why |
|---|---|---|---|
| handoff, session transfer | `skill-handoff` | OpenCode | Session handoff |
| research | `skill-research` | OpenCode | Research workflow |
| fact-check, skepticism | `skill-skeptic` | OpenCode | Validate claims |
| decisions log | `skill-decisions-log` | OpenCode | Log decisions |
| session log | `skill-session-log` | OpenCode | Full session logging |
| inquiry, interview | `skill-inquiry` | OpenCode | Structured inquiry |
| write manual, SOP | `skill-write-manual` | OpenCode | Create manuals/rules |
| reality check | `skill-reality-checker` | OpenCode | Verify files/API exist |
| code project template | `skill-code-project-template` | OpenCode | CLAUDE.md template |

## Autonomous

| Trigger | Skill | Source | Why |
|---|---|---|---|
| full autonomous pipeline | `lfg` | OpenCode | Plan → code → review → test → PR → CI |

## Notes

- Some skills have duplicates across ecosystems. The canonical version is kept; duplicates are in `_archive/`.
- Consider merging `ce-commit` into `ce-commit-push-pr`.
- Consider merging `skill-decisions-log` into `skill-session-log`.
