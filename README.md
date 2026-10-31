# MAFA-Agents: The AI Brain of MAFA

Multi-Agent Financial Advisor (MAFA) backend — a TRUE MCP-based multi-agent system for intelligent financial trading and investment analysis.

## What is MAFA?

MAFA is a **distributed multi-agent financial advisor system** built on the official Model Context Protocol (MCP). It coordinates specialized AI agents through structured communication to provide:

- 📊 Market analysis & LSTM-powered price predictions
- 💼 Real-time trade execution & portfolio management  
- 🎯 Personalized investment recommendations
- ⚠️ Risk analysis & rebalancing strategies
- 🔄 Multi-agent orchestration with intelligent routing

## MAFA Ecosystem

This repository is the **core agent backend** and connects with:

| Repository | Purpose | Tech |
|-----------|---------|------|
| [**MAFA-agents**](https://github.com/s0meshnalla/MAFA-agents) | MCP agent servers & orchestrator (THIS REPO) | Python (MCP protocol) |
| [**MCP_Financial_analyst_frontend**](https://github.com/s0meshnalla/MCP_Financial_analyst_frontend) | Web UI for user interaction | JavaScript/React |
| [**MAFA-B**](https://github.com/V-Satwik-Reddy/MAFA-B) | Backend API & database integration | Java/Spring |

### System Architecture

```
┌──────────────────────────────────────────────────────┐
│          MAFA-Agents (THIS REPO)                     │
│  MCP Servers + Orchestrator + Event Bus              │
├──────────────────────────────────────────────────────┤
│                                                      │
│  ┌─────────────┐  ┌──────────────┐  ┌────────────┐ │
│  │   Market    │  │  Execution   │  │ Portfolio  │ │
│  │   Research  │  │   & Trading  │  │  Analysis  │ │
│  └─────────────┘  └──────────────┘  └────────────┘ │
│                                                      │
│  ┌──────────────────────────────────────────────┐   │
│  │  Investment Strategy & Rebalancing Agent     │   │
│  └──────────────────────────────────────────────┘   │
├──────────────────────────────────────────────────────┤
│  Data Layer: Supabase Vector DB | Redis | LSTM     │
└──────────────────────────────────────────────────────┘
           ↕ FastAPI REST/WebSocket
┌──────────────────────────────────────────────────────┐
│          MAFA-B (Java Backend)                       │
│  REST API Gateway | Authentication | Persistence    │
└──────────────────────────────────────────────────────┘
           ↕ HTTP
┌──────────────────────────────────────────────────────┐
│    Frontend UI (MCP_Financial_analyst_frontend)      │
│  React Dashboard | Real-time Charts | User Portal    │
└──────────────────────────────────────────────────────┘
```

## Quick Start

### 1. Prerequisites

Ensure you have installed:

- **Python 3.11+** ([Download](https://www.python.org/downloads/))
- **Docker** ([Download](https://www.docker.com/products/docker-desktop)) — for Redis
- **Git** — for cloning repositories

### 2. Clone Repository

```bash
git clone https://github.com/s0meshnalla/MAFA-agents.git
cd MAFA-agents
```

### 3. Set Up Environment

#### Create Virtual Environment

**Windows:**
```bash
python -m venv .venv
.venv\Scripts\activate
```

**macOS/Linux:**
```bash
python -m venv .venv
source .venv/bin/activate
```

#### Install Dependencies

```bash
pip install -r requirements.txt
```

### 4. Configure API Keys & Environment

Copy the example environment file:
```bash
cp .env.example .env
```

Edit `.env` and add your API keys:

```env
# OpenRouter LLM Configuration (REQUIRED)
OPENROUTER_API_KEY=your_openrouter_api_key_here
OPENROUTER_MODEL=openai/gpt-4o-mini

# Supabase Vector Database (REQUIRED)
SUPABASE_URL=https://your-project.supabase.co
SUPABASE_API_KEY=your_supabase_key_here

# Google Custom Search (REQUIRED for market news)
CUSTOM_SEARCH_API_KEY=your_google_search_api_key_here
CUSTOM_SEARCH_CX=your_search_engine_id_here

# Redis Event Bus (REQUIRED)
REDIS_URL=redis://localhost:6379

# Optional: Yahoo Finance Fallback
USE_FALLBACK_DATA=true
```

**How to get API keys:**

| Key | Source | Steps |
|-----|--------|-------|
| `OPENROUTER_API_KEY` | [OpenRouter](https://openrouter.ai) | Sign up → API Keys → Copy key |
| `SUPABASE_URL`, `SUPABASE_API_KEY` | [Supabase](https://supabase.com) | Create project → Settings → API keys |
| `CUSTOM_SEARCH_API_KEY`, `CUSTOM_SEARCH_CX` | [Google Cloud Console](https://console.cloud.google.com) | Enable Custom Search API → Create credentials & search engine |

### 5. Start Required Services

#### Start Redis (Docker)

```bash
# Pull Redis image
docker pull redis:alpine

# Run Redis container
docker run -d --name mafa-redis -p 6379:6379 redis:alpine

# Verify Redis is running
docker ps | grep mafa-redis
```

**Alternative: If Docker is not available**, use [Redis from Windows Store](https://github.com/microsoftarchive/redis/releases) or install [Redis Homebrew](https://brew.sh/) on macOS.

#### Start the API Server

**Windows:**
```bash
.\start_server.ps1
```

**macOS/Linux:**
```bash
python -m uvicorn API:app --host 0.0.0.0 --port 5001
```

Server will be available at: `http://localhost:5001`

### 6. Verify Installation

```bash
# Health check (should show "operational")
curl http://localhost:5001/health

# List available MCP servers
curl http://localhost:5001/mcp/servers
```

## Project Structure

```
MAFA-agents/
├── API.py                      # FastAPI entry point
├── mcp_orchestrator.py         # MCP coordination & LLM routing
├── event_bus.py                # Redis pub/sub messaging
├── vectordbsupabase.py         # Supabase Vector DB client
├── monitoring.py               # Logging & metrics
├── http_client.py              # HTTP utilities
│
├── mcp_servers/                # MCP Protocol Servers (stdio)
│   ├── market_research_server.py    # LSTM predictions, news
│   ├── execution_server.py          # Trade execution, balances
│   ├── portfolio_server.py          # Portfolio analysis, risk
│   └── strategy_server.py           # Recommendations, rebalancing
│
├── agents/                     # Legacy LangChain agents (fallback)
│   ├── general_agent.py
│   ├── market_search_agent.py
│   ├── execution_agent.py
│   ├── portfolio_manager_agent.py
│   └── investment_strategy_agent.py
│
├── tools/                      # Agent tool definitions
│   ├── market_research_tools.py
│   ├── execute_trade_tools.py
│   ├── investment_strategy_tools.py
│   ├── memory_tools.py
│   └── profile_tools.py
│
├── lstm/                       # Prediction Models
│   ├── predict_next_day.py
│   ├── infer.py
│   └── output/                 # Pre-trained models (AAPL, MSFT, etc.)
│
├── requirements.txt            # Python dependencies
├── .env.example                # Environment template
├── start_server.ps1            # Windows startup script
└── logs/                       # Application logs
```

## Core Components

### MCP Servers

Each server exposes tools via the official MCP stdio protocol:

- **market_research_server.py** - LSTM price forecasting, real-time news aggregation
- **execution_server.py** - Trade execution, account balance, holdings verification
- **portfolio_server.py** - Portfolio snapshots, allocation analysis, risk metrics
- **strategy_server.py** - AI investment recommendations, portfolio rebalancing

### MCP Orchestrator

Routes user queries to appropriate agents based on intent classification:
- Analyzes natural language input
- Classifies intent (market, execution, portfolio, strategy)
- Selects and routes to MCP servers
- Returns results via event bus

### Event Bus (Redis)

Asynchronous event streaming for real-time updates:
- Market predictions
- Trade execution results
- Portfolio updates
- System alerts

### Vector Database (Supabase)

Stores agent memory and conversation history for context persistence.

## Supported Tickers

Pre-trained LSTM models available for: `AAPL` `AMZN` `ADBE` `GOOGL` `IBM` `JPM` `META` `MSFT` `NVDA` `ORCL` `TSLA`

## Key Features

✅ **TRUE MCP Protocol** — Official Model Context Protocol with stdio transport  
✅ **Multi-Agent Orchestration** — Intelligent routing to specialized agents  
✅ **LSTM Price Predictions** — Pre-trained neural networks for 11 major stocks  
✅ **Real-time Event Streaming** — Redis pub/sub + WebSocket connections  
✅ **Fallback Data Sources** — Yahoo Finance when primary broker unavailable  
✅ **Production Monitoring** — Structured JSON logging, health checks, metrics  
✅ **Rate Limiting** — 100 requests/minute per IP  
✅ **Input Validation** — Pydantic models with sanitization  

## Troubleshooting

### Redis Connection Error
```
Error: ConnectionRefusedError: [Errno 111] Connection refused
```
**Solution:** Ensure Redis container is running:
```bash
docker ps | grep mafa-redis
docker start mafa-redis  # if it's stopped
```

### Missing API Keys
```
Error: KeyError: 'OPENROUTER_API_KEY'
```
**Solution:** Add all required keys to `.env` file and ensure it's in the project root.

### Port Already in Use
```
Error: Address already in use
```
**Solution:** Use a different port:
```bash
python -m uvicorn API:app --host 0.0.0.0 --port 5002
```

## Environment Variables Reference

| Variable | Required | Default | Description |
|----------|----------|---------|-------------|
| `OPENROUTER_API_KEY` | ✅ Yes | — | LLM API key for agent orchestration |
| `OPENROUTER_MODEL` | ✅ Yes | — | Model ID (e.g., `openai/gpt-4o-mini`) |
| `SUPABASE_URL` | ✅ Yes | — | Supabase project URL |
| `SUPABASE_API_KEY` | ✅ Yes | — | Supabase service key |
| `CUSTOM_SEARCH_API_KEY` | ✅ Yes | — | Google Custom Search API key |
| `CUSTOM_SEARCH_CX` | ✅ Yes | — | Custom Search Engine ID |
| `REDIS_URL` | ✅ Yes | `redis://localhost:6379` | Redis connection string |
| `USE_FALLBACK_DATA` | ❌ No | `true` | Enable Yahoo Finance fallback |
| `LOG_LEVEL` | ❌ No | `INFO` | Logging level (DEBUG, INFO, WARNING) |

## Integration with MAFA Ecosystem

### Connecting to Frontend

1. **Frontend** calls MAFA-B (Java backend) with user queries
2. **MAFA-B** forwards requests to MAFA-agents `/mcp/query` endpoint
3. **MAFA-agents** orchestrates MCP servers and returns results
4. Results stream to **Frontend** via WebSocket or REST

### Connecting to MAFA-B (Java)

Add to MAFA-B `application.properties`:
```properties
mafa.agents.url=http://localhost:5001
mafa.agents.timeout=30000
```

Then call from MAFA-B:
```java
RestTemplate.postForObject(
  "http://localhost:5001/mcp/query",
  new MCPQueryRequest(userQuery, userId),
  MCPQueryResponse.class
);
```

## License

MIT License — See LICENSE file for details

## Support & Contribution

- 🐛 **Issues:** Report bugs on [GitHub Issues](https://github.com/s0meshnalla/MAFA-agents/issues)
- 🤝 **Contributing:** Fork → Branch → Push → Pull Request
- 📧 **Questions:** Refer to project documentation or create a discussion

---

**Made with ❤️ for intelligent financial decision-making**
