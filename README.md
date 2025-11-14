# Local AI Agent Development

A comprehensive framework for building and experimenting with local AI agents using Large Language Models (LLMs). This project explores different agent reasoning patterns, tool integration, and autonomous task execution for development automation.

## Overview

This project implements and compares two fundamental AI agent architectures:

1. **Chain of Thought (CoT) Agent** - Plans entire task upfront, then executes sequentially
2. **ReAct Agent** - Iterative reasoning with Think → Act → Observe → Repeat cycle

Both agents are equipped with powerful development tools and can autonomously set up projects, generate code, manage files, and execute shell commands - all running locally with Ollama.

## Project Structure

```
dev-setup-agent/
├── notebooks/
│   ├── cot_agent.ipynb           # Chain of Thought agent implementation
│   ├── react_agent.ipynb         # ReAct agent implementation
│   └── agent_comparison.ipynb    # Performance comparison & analysis
├── requirements.txt              # Python dependencies
├── .env.example                  # Environment configuration template
└── README.md                     # This file
```

## Agent Architectures

### Chain of Thought (CoT) Agent

**Pattern:** Think (plan all steps) → Execute all → Report

**Characteristics:**
- Generates complete plan before any execution
- Executes all steps sequentially without re-planning
- Requires only 1 LLM call for planning vs N calls in iterative approaches
- Uses placeholder system to reference generated content between steps
- Better for deterministic, well-defined tasks

**Advantages:**
- ⚡ Faster execution (fewer LLM calls)
- 💰 Lower cost
- 🔍 Simpler to debug (plan visible upfront)
- 📋 Better for standard setup tasks

**Disadvantages:**
- ❌ Cannot adapt if plan is wrong
- 🚫 No error recovery
- 🔒 Less suitable for exploratory tasks

**Best Use Cases:**
- Project scaffolding and boilerplate generation
- Standard development environment setup
- Tasks with known, sequential steps
- Code generation from templates

### ReAct Agent

**Pattern:** Think → Act → Observe → Think → Act...

**Characteristics:**
- Iterative reasoning loop with continuous adaptation
- Executes one action at a time, observes results, then decides next step
- Context memory system stores generated content for reuse
- Smart placeholder detection and replacement
- Can recover from errors and adapt to unexpected results

**Advantages:**
- 🔄 Adaptive to changing conditions
- 🛠️ Error recovery capabilities
- 🧠 Better for complex problem-solving
- 🔍 Exploratory task handling

**Disadvantages:**
- 🐢 Slower (multiple LLM calls)
- 💸 Higher cost
- 🔄 More complex implementation
- 📈 Growing context size

**Best Use Cases:**
- Debugging unknown issues
- Exploratory development tasks
- Complex problem-solving requiring adaptation
- Tasks with uncertain outcomes

## Available Tools

Both agents have access to the following tools:

### 1. File System Tool
Perform safe file operations within a sandboxed environment:
- `create` - Create new files with content
- `read` - Read file contents
- `write` - Write/overwrite file contents
- `list` - List files in a directory
- `mkdir` - Create directories

### 2. Shell Tool
Execute whitelisted shell commands safely:
- Allowed commands: `ls`, `cat`, `echo`, `pwd`, `mkdir`, `touch`, `git`, `python`, `pip`, `npm`, `poetry`
- Runs in sandbox directory with 30s timeout
- Command safety validation

### 3. Code Generator Tool
Generate boilerplate code from templates:
- `fastapi_main` - FastAPI application with CORS, health checks
- `requirements` - Python dependencies (FastAPI, AWS, testing)
- `dockerfile` - Lambda-compatible Dockerfile
- `gitignore` - Standard Python .gitignore

Templates support variable substitution for customization.

## Getting Started

### Prerequisites

- Python 3.9+
- [Ollama](https://ollama.ai/) installed and running
- LLM model pulled (default: `llama3.1:8b`)

### Installation

1. Clone the repository:
```bash
git clone <repository-url>
cd dev-setup-agent
```

2. Create and activate virtual environment:
```bash
python -m venv .venv
source .venv/bin/activate  # On Windows: .venv\Scripts\activate
```

3. Install dependencies:
```bash
pip install -r requirements.txt
```

4. Set up environment variables:
```bash
cp .env.example .env
# Edit .env with your configuration
```

5. Start Ollama:
```bash
ollama serve
```

6. Pull the LLM model:
```bash
ollama pull llama3.1:8b
```

### Usage

Open the Jupyter notebooks to explore and test the agents:

```bash
jupyter notebook
```

#### Testing Chain of Thought Agent

Open `notebooks/cot_agent.ipynb` and run the cells. Example task:

```python
agent = ChainOfThoughtAgent(llm=llm, tools=[fs_tool, shell_tool, code_gen_tool])
task = "Create a FastAPI project with main.py, requirements.txt, and .gitignore"
result = agent.run(task)
```

#### Testing ReAct Agent

Open `notebooks/react_agent.ipynb` and run the cells. Example task:

```python
agent = DevSetupAgent(llm=llm, tools=[fs_tool, shell_tool, code_gen_tool])
task = "Create a FastAPI project and verify all files are created correctly"
result = agent.run(task)
```

#### Comparing Agents

Open `notebooks/agent_comparison.ipynb` to see side-by-side performance comparisons, metrics, and visualizations.

## Configuration

Edit `.env` file to customize:

```bash
# LLM Configuration
LLM_PROVIDER=ollama           # or openai, anthropic
OLLAMA_MODEL=llama3.1:8b      # Model to use

# Agent Configuration
MAX_ITERATIONS=15             # Max steps for ReAct agent
SANDBOX_DIR=./sandbox         # Safe execution directory
ENABLE_SHELL_EXECUTION=true   # Allow shell commands

# Logging
LOG_LEVEL=INFO                # DEBUG, INFO, WARNING, ERROR
```

## Performance Comparison

| Aspect | Chain of Thought | ReAct |
|--------|------------------|-------|
| **Pattern** | Think (all) → Execute → Report | Think → Act → Observe → Loop |
| **LLM Calls** | 1 planning call + execution | N calls (one per iteration) |
| **Speed** | ⚡ Faster | 🐢 Slower |
| **Adaptability** | ❌ Cannot adapt | ✅ Adapts to observations |
| **Cost** | 💰 Lower | 💸 Higher |
| **Error Recovery** | ❌ Fails if plan wrong | ✅ Can recover |
| **Best For** | Standard tasks | Complex tasks |

## Key Features

- **Local-First**: Runs entirely on your machine with Ollama
- **Safe Execution**: Sandboxed file system and command whitelisting
- **Rich Feedback**: Beautiful terminal output with progress tracking
- **Extensible**: Easy to add new tools and capabilities
- **Well-Documented**: Comprehensive notebooks with explanations
- **Production-Ready Tools**: Real-world code generation templates

## Dependencies

Core dependencies:
- `pydantic` - Data validation and schema definition
- `python-dotenv` - Environment configuration
- `httpx` - HTTP client for Ollama API
- `rich` - Beautiful terminal output
- `jupyter` - Interactive notebooks
- `matplotlib`, `seaborn` - Visualization

## Examples

### Example 1: Create FastAPI Project (CoT)

The CoT agent will plan all steps upfront:

1. Create project directory
2. Generate FastAPI main.py code
3. Write main.py to disk
4. Generate requirements.txt
5. Write requirements.txt to disk
6. Generate .gitignore
7. Write .gitignore to disk
8. List directory to verify

All steps executed in sequence with 1 planning call.

### Example 2: Debug and Fix Project (ReAct)

The ReAct agent will iteratively explore and fix:

1. List files to understand structure
2. Read files to find issues
3. Generate fix
4. Apply fix
5. Verify fix worked
6. Adapt if errors occur

Each step informed by previous observations.

## Troubleshooting

### Ollama Connection Issues

```bash
# Check if Ollama is running
curl http://localhost:11434/api/tags

# Start Ollama
ollama serve

# Verify model is available
ollama list
```

### Import Errors

```bash
# Reinstall dependencies
pip install -r requirements.txt --upgrade
```

### Sandbox Permissions

The sandbox directory is created automatically. If you encounter permission errors:

```bash
chmod -R 755 ./sandbox
```

## Contributing

This is an experimental project for learning and exploring AI agent architectures. Contributions, ideas, and feedback are welcome!

## License

MIT

---

## Next Steps

### 🚀 Craft a Hybrid Agent

The next evolution of this project is to combine the best of both worlds:

**Hybrid Agent Architecture:**
- Use **Chain of Thought** for initial planning (fast, cost-effective)
- Switch to **ReAct** mode when errors occur (adaptive, robust)
- Implement checkpointing to recover from failures
- Add confidence scoring to determine which mode to use

**Benefits:**
- Optimal speed and cost for standard tasks
- Robust error recovery for edge cases
- Best of both reasoning patterns
- Production-grade reliability

### 🏭 Production Deployment

Transform the experimental notebooks into a production-ready system:

**Planned Features:**
1. **CLI Interface** - Command-line tool for agent execution
2. **API Server** - FastAPI service to run agents via HTTP
3. **Multi-LLM Support** - OpenAI, Anthropic, local models
4. **Persistent State** - Save and resume agent sessions
5. **Advanced Observability** - Logging, metrics, tracing
6. **Security Hardening** - Enhanced sandboxing, audit logs
7. **Tool Marketplace** - Plugin system for community tools
8. **Batch Processing** - Queue multiple tasks
9. **Web UI** - Interactive dashboard for monitoring
10. **Docker Deployment** - Containerized for easy deployment

**Timeline:**
- Phase 1: Hybrid Agent Implementation (2 weeks)
- Phase 2: Production Infrastructure (4 weeks)
- Phase 3: Advanced Features & Polish (4 weeks)

Stay tuned for updates as we evolve from research prototype to production system!