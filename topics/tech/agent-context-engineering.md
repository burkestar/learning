# Agent Context Engineering

## Principles
- "right information to right agent at right time"
- high signal tokens that maximize likelihood of desired outcome

## Trends
 
- Context windows will grow
	- 4K tokens (GPT-3, 2020) → 128K (2023) → 1M+ (2025-2026)  -> 10M Frontier (2027) - 150-250% CAGR - step function not linear/exponential smoothed growth
	- Better hardware and, better attention algorithms, ring attention
	- Effective context is shorted than advertised context window due to U-shaped attention curve 
- Model performance
	- Benchmarks shifting from static knowledge tests to real-world task performance (SWE-Bench)
	- Open weight models are converging on frontier model performance
	- Specialization of models for tasks with Mixture of Experts (MoE) architecture to combine models
- Tokens costs declining
	- ~60-90%/year (roughly -70% to -75% CAGR)
	- dropped from $2 to $0.50 per million tokens in a year
	- fallen 280x over two years
- Token consumption is outpacing decline in token costs so AI costs will continue to increase (Jevon's Paradox - cheaper resource leads to more consumption)
	- Goldman Sachs projects token consumption will multiply 24x — from ~2026 to 2030 (roughly a 108% CAGR)Token consumption is expected to multiply 24 times, to 120 quadrillion tokens per month, between 2026 and 2030
	- Net effect: enterprise AI spend rose 320% over two years despite the 280x price drop
- Local inference is viable for real world use cases
- Memory bandwidth not compute is the bottleneck for high throughput workloads
- Model routing/orchestration is core architecture pattern
	- routing tasks to appropriate models based on task complexity and domain specialization
- Observability and explainability is required capability
- GraphRAG
- Semantic Layer

## Frameworks

- [mem0](https://docs.mem0.ai/platform/overview) 
- [supermemory](https://supermemory.ai/) 
	- Claims to be #1 in memory benchmarks (not verified)
	- Implemented in Typescript with wrappers for various frameworks, most use an SDK client and API
	- Claude plugin with hooks to manage memories
	- Organizes memories by project
	- Core engine is closed source, only SDKs are open source
	-  Not clear if using GraphRAG and graph database
- [membase](https://docs.membase.so/getting-started/quickstart)
- [graphlit](https://www.graphlit.com/)
- [zep](https://help.getzep.com/graph-overview) (Graphiti)
	- designed for dynamic and frequently updated datasets with continuous data updates, temporal fact tracking, and sub-200ms query latency
- [Hindsight](https://docs.hindsight.vectorize.io/)
- [mem9](https://mem9.ai/)
- [kumiho](https://kumiho.io/en/use-cases/ai-cognitive-memory)
- [memora](https://www.memora.sh/)
	- Sub-50ms recall
- [cognee](https://docs.cognee.ai/)
- Framework native
	- [crewai memory](https://docs.crewai.com/v1.15.1/en/concepts/memory)
	- [llamaindex memory](https://developers.llamaindex.ai/python/framework/module_guides/deploying/agents/memory/)
	- Anthropic [Managing Context on Claude Developer Platform](https://claude.com/blog/context-management)
	- [OpenAI Agent Memory SDK](https://openai.github.io/openai-agents-python/sandbox/memory/)

## Benchmarks
- [MemoryBench](https://supermemory.ai/docs/memorybench/overview)
- [LongMemEval](https://github.com/xiaowu0162/LongMemEval)
- [Locomo](https://github.com/snap-research/locomo) 
- [Convomem](https://github.com/SalesforceAIResearch/ConvoMem)
- https://arxiv.org/abs/2510.27246
- https://arxiv.org/abs/2504.19413

## Research Notes

### Articles

- [Components of a Coding Agent](https://magazine.sebastianraschka.com/p/components-of-a-coding-agent) 
	- repo navigation, search, function lookup, diff application, test execution, error inspection, and keeping all the relevant information in context
	- Agent: a loop that uses a model plus tools, memory, and environment feedback
	- Agent harness: the software scaffold around an agent that manages context, tool use, prompts, state, and control flow
	- Build Repo Context
		- Git repo
		- Project docs and agent instructions (AGENTS.md)
		- Builds stable prompt prefix based on rules, tools, and workspace summary
- [Survey of AI Agent Memory Frameworks](https://www.graphlit.com/blog/survey-of-ai-agent-memory-frameworks) 
	- In 2024, memory meant "keep more chat history, summarize old messages, or retrieve chunks from a vector database"
	- state of the art in AI agent memory has moved toward structured, scoped, temporal, self-editable context systems.
	- Question is:
		- What should the agent know right now
		- Where did that knowledge come from (provenance)
		- Is it still true (validity, temporality)
		- Who is allowed to see it (access)
		- How should it be assembled into context for this task
	- Types of memory and context
		- Memory APIs, where applications store and retrieve user, agent, session, or organization memories.
		- Universal memory layers, where many AI tools share one personal or team memory.
		- Temporal knowledge graphs, where facts and relationships evolve over time.
		- Framework-native memory, where memory is part of LangGraph, CrewAI, LlamaIndex, or another agent loop.
		- File or workspace memory, where coding and research agents save durable notes outside the context window.
		- Context platforms, where agents retrieve source-grounded operational context from real systems.
	- Memory is durable information the system can preserve across turns, sessions, tasks, users, or agents
	- Context is the selected working set the model receives for a specific task. It includes instructions, recent messages, retrieved evidence, tool schemas, user state, organization facts, permissions, citations, intermediate results, and sometimes memories.
	- Memory - what should persist?
	- Retrieval - what should be found?
	- Context - what should be shown to the model now?
	- "Memory becomes useful only when the right pieces are retrieved, ranked, filtered, transformed, and inserted into the active context window at the right time."
	- Shifts
		- Chat history is not enough
		- Temporal memory
		- Self-editing memory (correct, consolidate, prune)
		- Benchmarks moved past recall
			- different memory abilities: temporal reasoning, multi-session continuity, user feedback learning, workspace experience, long-horizon recall, and procedural improvement.
		- context engineering is the product
			- - Selecting the right source evidence.
			- Filtering by user, tenant, project, or permission.
			- Resolving entity identity across systems.
			- Preferring current facts without deleting historical ones.
			- Keeping citations and provenance attached.
			- Compressing or omitting stale tool results.
			- Loading memories only when they are relevant.
	- Separate Agent Runtime and Context Layer
		- Why?
			- This lets you change models, orchestration frameworks, and agent patterns without rebuilding the organizational memory substrate.
		- The agent runtime owns:
			- Task planning.
			- Tool selection.
			- Reasoning loops.
			- Short-term working memory.
			- When to retrieve context.
			- When to write back memories, notes, or decisions.
		- The context layer owns
			- Source connectors and continuous sync.
			- Multimodal ingestion and extraction.
			- Entity resolution.
			- Temporal facts and observations.
			- Search, ranking, and graph traversal.
			- Permissions and tenant isolation.
			- Citations and provenance.
			- Durable APIs for many agents and applications.

### Videos

- [Context Engineering for Agents](https://www.youtube.com/watch?v=4GiqzUHD5AA) Langchain 2025 July

## Notes

- Context
	- Organizational
		- Business - strategy docs, memos, 6 pagers, strategic initiatives, OKRs
		- Market - markets served, positioning, competitors, key market trends, key market reports
		- Customers
		- Departments and teams
		- People
	- Technical
		- PRDs, requirements
		- Prototypes
		- Technical designs and Architecture
		- Infrastructure
		- Standards - industry, company, team - security, compliance, architecture
		- Tech stack - languages, frameworks
		- Service definitions (APIs, purpose)
		- Data catalog
		- Documentation - user guides, API specs, tutorials, developer guides
	- Initiatives and projects
		- Goals, strategy, vision, roadmap
		- Projects
	- Decisions
	- Personal
		- Preferences
		- Skills and habits
		- Responsibilities and job function
		- Career growth / performance
		- Meeting notes
- Context-aware computing
	- **Core dimensions:**
		- **Temporal stability**: static (rarely changes) → dynamic (changes routinely) → ephemeral (valid for seconds/minutes, e.g. "user is typing")
		- **Volatility/decay rate**: how fast the value becomes stale (half-life concept)
		- **Scope**: personal/local vs. shared/global
		- **Provenance**: sensed (device/API) vs. declared (user-stated) vs. derived/inferred (computed from other context)
		- **Certainty**: deterministic fact vs. probabilistic/inferred estimate
		- **Granularity**: atomic (single fact) vs. aggregated/composite
		- **Primary vs. secondary context** (Dey & Abowd model): primary = who/what/when/where; secondary = anything derivable from primary
		- **Persistence**: transient (session-only) vs. persistent (stored long-term)
	- **Classic reference models:**
		- **Dey & Abowd (2001)** — foundational context-aware computing taxonomy: identity, location, activity, time as primary categories
			- [Towards a Better Understanding of Context and Context-awareness](https://www.cs.umd.edu/class/spring2025/cmsc818G/files/contextdey.pdf) 
			- "A system is context-aware if it uses context to provide relevant information and/or services to the user, where relevancy depends on the user’s task."
			- Primary context acts as an **index** into other relevant context — you rarely query "activity" in isolation; you use identity/location/time/activity together to _pull_ secondary context
				- Structure context storage so primary fields are indexed/queryable
				- Secondary context should be computed/fetched on-demand via primary keys, not stored redundantly
		- **Schilit et al.** — splits context into _computing context_, _user context_, _physical context_, _time context_
		- **Nonaka's KM model** — tacit vs. explicit knowledge (different axis: expressibility, not time)
- Coding Agent Harness in Dey and Abowd Context Model
	- **Primary context (the indices):**
		- **Identity** → which agent/session, which user, which repo/project
		- **Location** → current working directory, active file, branch, environment (local/CI/sandbox)
		- **Activity** → current task/subtask being executed (e.g., "fixing test failure," "writing function")
		- **Time** → turn number, timestamp, position in conversation/task sequence
	- **Secondary context (looked up via primary keys):**
		- **Memory** — indexed by identity + time: conversation history, prior tool results, CLAUDE.md/AGENTS.md config, learned preferences
		- **Tools** — indexed by activity + location: which tools are relevant/available given current task and working directory (e.g., git tools only matter if location = repo)
		- **Prompts/instructions** — indexed by identity + activity: system prompt = identity-level (stable), task prompt = activity-level (per-turn)
		- **Code/file state** — indexed by location: file contents, diffs, test results
- Challenges
	- Access control (ACL hydration) to track across systems
