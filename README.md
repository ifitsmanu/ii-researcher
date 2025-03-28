# II Deep Search

A powerful deep search agent that uses BAML functions to perform intelligent web searches and generate comprehensive answers to questions.

## Features

- Intelligent web search using Tavily and SerpAPI search providers
- Web scraping and content extraction with multiple providers (Firecrawl, Browser, BS4, Tavily)
- Multi-step reasoning and reflection
- Configurable LLM models for different tasks
- Asynchronous operation for better performance
- Comprehensive answer generation with references
- Performance monitoring with Langfuse

## Prerequisites

- Python 3.7+ (for local development)
- Docker and Docker Compose (for containerized deployment)
- OpenAI API key
- Tavily API key (for web search and extraction)
- SerpAPI API key (alternative search provider)
- Firecrawl API key (for web scraping)
- Langfuse keys (for monitoring)
- Node.js and npm (for local frontend development)

## Installation and Setup

1. Clone the repository:

```bash
git clone https://github.com/Intelligent-Internet/ii_reseacher.git
cd ii_reseacher
```

2. Install the package in development mode:

```bash
pip install -e .
```

3. Set up your environment variables:

````bash
# API Keys
export OPENAI_API_KEY="your-openai-api-key"
export TAVILY_API_KEY="your-tavily-api-key" # set this api key when you select SEARCH_PROVIDER is tavily
export SERPAPI_API_KEY="your-serpapi-api-key"  # set this api key when you select SEARCH_PROVIDER is serpapi
export FIRECRAWL_API_KEY="your-firecrawl-api-key"  # set this api key when you select SCRAPER_PROVIDER is firecrawl

# API Endpoints
export OPENAI_BASE_URL="http://localhost:4000"

# Compress Configuration
export COMPRESS_EMBEDDING_MODEL="text-embedding-3-large"
export COMPRESS_SIMILARITY_THRESHOLD="0.3"
export COMPRESS_MAX_OUTPUT_WORDS="4096"
export COMPRESS_MAX_INPUT_WORDS="32000"

# Search and Scraping Configuration
export SEARCH_PROVIDER="serpapi"  # Options: 'serpapi' | 'tavily'
export SCRAPER_PROVIDER="firecrawl"  # Options: 'firecrawl' | 'bs' | 'browser' | 'tavily_extract'

# Timeouts and Performance Settings
export SEARCH_PROCESS_TIMEOUT="300"  # in seconds
export SEARCH_QUERY_TIMEOUT="20"     # in seconds
export SCRAPE_URL_TIMEOUT="30"       # in seconds
export STEP_SLEEP="100"              # in milliseconds


Config env when using compress by LLM
```bash
export USE_LLM_COMPRESSOR="TRUE"
export FAST_LLM="gemini-lite" # The model use for context compression
````

Config env when run with **Pipeline**:

```bash
# Model Configuration
export STRATEGIC_LLM="gpt-4o" # The model use for choose next action
export SMART_LLM="gpt-4o" # The model use for others tasks in pipeline
```

Config env when run with **Reasoning**:

```bash
export R_MODEL=r1 # The model use for reasoning
export R_TEMPERATURE=0.2 # Config temperature for reasoning model
export R_REPORT_MODEL=gpt-4o # The model use for writing report
export R_PRESENCE_PENALTY=0 # Config presence_penalty for reasoning model
```

1. Configure and Run LiteLLM (Local LLM Server):

```bash
# Install LiteLLM
pip install litellm

# Create litellm_config.yaml file
cat > litellm_config.yaml << EOL
model_list:
  - model_name: gpt-4o
    litellm_params:
      model: gpt-4o
      api_key: ${OPENAI_API_KEY}
  - model_name: o1-mini
    litellm_params:
      model: o1-mini
      api_key: ${OPENAI_API_KEY}
  - model_name: r1
    litellm_params:
      model: deepseek-reasoner
      api_key: ${OPENAI_API_KEY}

litellm_settings:
  drop_params: true
EOL

# Start LiteLLM server
litellm --config litellm_config.yaml
```

The LiteLLM server will run on http://localhost:4000 by default.

5. Install and Run Backend API:

```bash
# Start the API server
python api.py
```

The API server will run on http://localhost:8000

6. Install and Run Frontend:

```bash
# Navigate to frontend directory
cd frontend

# Install dependencies
npm install

# Start the development server
npm run dev
```

The frontend will be available at http://localhost:3000

## Usage

### Using the Web Interface

1. Open your browser and navigate to http://localhost:3000
2. Enter your question in the search box
3. View the real-time search results and final answer

### Using the CLI

Run the deep search agent with your question:

There are two modes:

- **Pipeline Mode**: This mode is suitable for general questions and tasks.

```bash
python cli.py --question "your question here"
```

- **Reasoning Mode**: This mode is suitable for complex questions and tasks.

```bash
python cli.py --question "your question here" --use-reasoning --stream
```

## Run with Docker

1. **Important**: Make sure you have set up all environment variables from step 3 before proceeding.

2. Start the services using Docker Compose:

```bash
# Build and start all services
docker compose up --build -d
```

The following services will be started:

- frontend: Next.js frontend application
- api: FastAPI backend service
- litellm: LiteLLM proxy server

The services will be available at:

- Frontend: http://localhost:3000
- Backend API: http://localhost:8000
- LiteLLM Server: http://localhost:4000

3. View logs:

```bash
# View all logs
docker compose logs -f

# View specific service logs
docker compose logs -f frontend
docker compose logs -f api
docker compose logs -f litellm
```

4. Stop the services:

```bash
docker compose down
```

## Running QwQ Model with SGLang

To run the Qwen/QwQ-32B model using SGLang, use the following command:

```bash
python3 -m sglang.launch_server --model-path Qwen/QwQ-32B --host 0.0.0.0 --port 30000 --tp 8 --context-length 131072
```
