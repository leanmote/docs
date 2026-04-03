# Insight Agent (LangGraph)

Multi-agent pipeline for investigating software development problems, generating Cypher queries, and simulating execution with human-in-the-loop validation.

## Overview

The Insight Agent is a sophisticated LangGraph-based system that automatically discovers software development inefficiencies, validates findings with humans, and generates Neo4j Cypher queries to measure them.

**Tech Stack:**
- LangGraph 0.2.75 - Pipeline orchestration
- LangChain 0.3.x - LLM integrations (OpenAI GPT-5.2, Gemini 3)
- FastAPI 0.115.6 - Web UI backend
- SQLAlchemy 2.0.35 - Database ORM (MySQL)
- Perplexity API - Search discovery
- Gemini API - Web-enabled research with grounding

## Architecture

```
User Input (Topic)
    │
    ▼
Research Agent
  ├─ Perplexity Search (6 queries)
  └─ Gemini URL Context Analysis
    │
    ▼
Problem Validator (Human-in-the-loop)
  ├─ Check sources
  └─ Human approval
    │
    ▼
Feasibility Analyzer
  ├─ Check against Neo4j schema
  └─ Gap analysis
    │
    ▼
Query Generator
  ├─ Build Cypher template
  └─ Add parameters
    │
    ▼
Human Validator
  └─ Approve/reject Cypher
    │
    ▼
Query Executor
  ├─ Simulate (default)
  └─ Return sensors
    │
    ▼
Database (Optional)
  └─ Persist insight + tool
```

## Directory Structure

```
insight_agent/
├── src/
│   ├── agents/           # Agent implementations
│   │   ├── research_agent.py
│   │   ├── problem_validation.py
│   │   ├── feasibility_analyzer.py
│   │   ├── query_generator.py
│   │   ├── human_validation.py
│   │   ├── query_executor.py
│   │   └── prompt_generator.py
│   ├── services/         # External integrations
│   │   ├── llm.py
│   │   ├── genai_client.py
│   │   ├── perplexity_search.py
│   │   └── prompt_loader.py
│   ├── domain/           # Shared state
│   │   └── state.py
│   ├── db/               # Database models
│   │   └── models.py
│   ├── graph.py          # LangGraph flow definition
│   ├── main.py           # CLI entry point
│   └── config.py         # Configuration
├── prompts/              # Editable LLM prompts
├── web/                  # FastAPI web interface
│   ├── main.py
│   ├── templates/
│   └── static/
├── scripts/              # Utility scripts
├── requirements.txt
└── README.md
```

## Agents

### Research Agent
Discovers real-world inefficiencies using web search and LLM synthesis.

**Flow:**
1. Generates 6 strategic search queries from the topic
2. Executes Perplexity Search for discovery
3. Ranks and selects top 12 URLs
4. Sends URLs to Gemini with `url_context` tool for deep analysis
5. Extracts structured insights with evidence

**State Updates:**
- `research_notes` - Raw Gemini response
- `research_sources` - List of evidence with URLs
- `research_insights` - Ranked insights with signals, evidence, tools
- `problem_statement`, `problem_root_cause`, `signals`

### Problem Validation Agent
Human-in-the-loop review of discovered problems.

**Features:**
- Auto-reject if no sources or generic sources
- Captures human feedback for refinement
- Configurable auto-approval via `AUTO_APPROVE_PROBLEM=true`

### Feasibility Analyzer Agent
Determines if research insights can be queried against the Neo4j schema.

**Output:**
- Feasible insights with confidence level and query hints
- Infeasible reports with gap analysis

### Query Generator Agent
Generates parameterized Cypher templates from feasible insights.

**Output Format:**
```json
{
  "tool_name": "PR Review Lead Time",
  "tool_description": "Analyzes pull request review delays...",
  "cypher_query": "MATCH (p:Person)-[:REVIEWED]->(pr:PullRequest)...",
  "parameters": [
    {"name": "orgId", "type": "string", "required": true}
  ],
  "reasoning": "...",
  "assumptions": ["..."]
}
```

### Human Validation Agent
CLI-based Cypher query review with approval/rejection workflow.

### Query Executor Agent
Simulates or executes the generated Cypher query.

**Modes:**
- Simulation (default) - Returns mock sensors
- Real execution - Requires Neo4j driver configuration

## Configuration

### Environment Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `OPENAI_API_KEY` | Required | OpenAI authentication |
| `LLM_MODEL` | gpt-4o-mini | Default OpenAI model |
| `GEMINI_API_KEY` | Optional | Gemini authentication |
| `GEMINI_MODEL` | gemini-3-flash-preview | Gemini text model |
| `GEMINI_WEB_MODEL` | gemini-3-pro-preview | Gemini web-enabled model |
| `GEMINI_WEB_TOOL` | google_search | Tool: google_search, url_context, or both |
| `PREFER_GEMINI_WEB` | true | Use Gemini with web tools for research |
| `PERPLEXITY_API_KEY` | Optional | Perplexity search |
| `PREFER_PERPLEXITY_SEARCH` | true | Use Perplexity for discovery |
| `PERPLEXITY_MAX_RESULTS` | 10 | Results per query |
| `SIMULATE_EXECUTION` | true | Simulate or execute queries |
| `AUTO_APPROVE_PROBLEM` | false | Skip problem validation |
| `AUTO_APPROVE_CYPHER` | false | Skip cypher validation |
| `MAX_PROBLEM_ATTEMPTS` | 3 | Max research iterations |
| `MAX_CYPHER_ATTEMPTS` | 3 | Max query generation iterations |
| `SHOW_PROGRESS` | true | Show progress spinners |
| `REQUIRE_SOFTWARE_SOURCES` | true | Reject generic sources |

### Database Configuration
- `DATABASE_HOST`, `DATABASE_USER`, `DATABASE_PASSWORD`, `DATABASE_NAME` for MySQL
- Supports RDS IAM authentication for AWS deployments

### Jira Integration
- `JIRA_API_TOKEN` - Required for epic creation
- `JIRA_PROJECT_KEY` or `JIRA_BOARD_URL` - Target project
- Scoped token mode: `JIRA_CLOUD_ID`
- Basic token mode: `JIRA_EMAIL` + `JIRA_BASE_URL`

## Usage

### CLI
```bash
pip install -r requirements.txt
python -m src.main "Problemas con lead time de cambios (DORA)"
```

### Web UI
```bash
pip install -r requirements.txt
uvicorn web.main:app --reload --port 8000
```
Access at `http://localhost:8000`

## Web API Endpoints

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/api/research` | POST | Start research, returns session_id |
| `/api/status/:session_id` | GET | Poll research progress |
| `/api/problem-validate` | POST | Approve/reject problem |
| `/api/analyze-feasibility` | POST | Run feasibility analysis |
| `/api/generate-cypher` | POST | Generate Cypher query |
| `/api/validate-cypher` | POST | Approve/reject Cypher |
| `/api/execute` | POST | Run query (simulated) |
| `/api/save-insight` | POST | Persist to database |
| `/api/upload-epic` | POST | Create Jira epic |

## Database Models

- **SavedInsight** - Stores feasible insights with feasibility analysis
- **SavedTool** - Stores generated tools (Cypher, parameters, reasoning)
- **SavedPrompt** - Stores generated prompts for Neo4j agents
- **SavedInfeasibleInsight** - Gap reports for infeasible insights

## Extending the System

1. **Real Query Execution** - Replace simulation in `query_executor.py` with Neo4j driver
2. **Custom Research Engine** - Alternative to Perplexity (e.g., custom scraper)
3. **Additional Validation** - Add domain-specific validators between agents
4. **Advanced Persistence** - Extend database models with custom fields
5. **Alternative LLMs** - Add support for Claude, Cohere, etc. in `llm.py`
6. **Webhook Integration** - Connect to n8n or Zapier for automated reactions

## Graph Visualization

Generate workflow diagrams:
```bash
python scripts/render_graph.py
```

Outputs:
- `graph.mmd` - Mermaid diagram
- `graph.png` - PNG image (if supported)
