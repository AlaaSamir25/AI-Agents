# Coffee Machine AI Agent

This notebook provides an AI-powered coffee machine procurement system designed to assist **Rankyx**, a company specializing in AI solutions for search and recommendation systems, in selecting the best coffee machines for their office. The system leverages search engines, web scraping, and AI agents to gather, analyze, and present data from various e-commerce platforms.

## Table of Contents

- [Overview](#overview)
- [Installation](#installation)
- [Setup](#setup)
- [Agents](#agents)
  - [Agent A: Search Queries Recommendation Agent](#agent-a-search-queries-recommendation-agent)
  - [Agent B: Search Engine Agent](#agent-b-search-engine-agent)
  - [Agent C: Web Scraping Agent](#agent-c-web-scraping-agent)
  - [Agent D: Procurement Report Author Agent](#agent-d-procurement-report-author-agent)
- [Running the AI Crew](#running-the-ai-crew)
- [Output](#output)
- [Dependencies](#dependencies)
- [License](#license)

## Overview

The **Coffee Machine AI Agent** is a multi-agent system that automates the process of finding, comparing, and recommending coffee machines for purchase. The system performs the following tasks:

- Generates search queries based on specific criteria.
- Searches for products on e-commerce websites.
- Scrapes product details from search results.
- Produces a professional procurement report in HTML format.

## Installation

To use this notebook, install the required dependencies by running:

```bash
pip install -r requirements.txt
```

## Setup

### Environment Variables

Before running the notebook, configure the following environment variables for API keys:

```python
import os
os.environ['GEMINI_API_KEY'] = 'ADD-YOUR-SECRET-KEY'
os.environ['AGENTOPS_API_KEY'] = 'ADD-YOUR-SECRET-KEY'
agentops.init(api_key='ADD-YOUR-SECRET-KEY', skip_auto_end_session=True)
search_client = TavilyClient(api_key='ADD-YOUR-SECRET-KEY')
scrape_client = Client(api_key="ADD-YOUR-SECRET-KEY")
basic_llm = LLM(model="gemini/gemini-pro", temperature=0)
```

### Output Directory

Ensure the following output directory exists to save results:

```python
output_dir = './ai-agent-output'
os.makedirs(output_dir, exist_ok=True)
```

## Agents

### Agent A: Search Queries Recommendation Agent

This agent generates search queries based on the product name, target websites, country, and language.

```python
search_queries_recommendation_agent = Agent(
    role='Search Queries Recommendation Agent',
    goal='To provide a list of suggested search queries for the search engine.',
    backstory='Assists in finding products by suggesting search queries.',
    llm=basic_llm,
    verbose=True
)
```

### Agent B: Search Engine Agent

This agent utilizes search queries to find products and collects results filtered by confidence score.

```python
search_engine_agent = Agent(
    role='Search Engine Agent',
    goal='To search for products based on suggested queries.',
    backstory='Finds products on specified websites using search queries.',
    llm=basic_llm,
    verbose=True,
    tools=[search_engine_tool]
)
```

### Agent C: Web Scraping Agent

This agent scrapes details from URLs collected by the Search Engine Agent, extracting information like title, price, and specifications.

```python
scraping_agent = Agent(
    role="Web Scraping Agent",
    goal="Extract product details from web pages.",
    backstory="Gathers data to identify the best products.",
    llm=basic_llm,
    tools=[web_scraping_tool],
    verbose=True
)
```

### Agent D: Procurement Report Author Agent

This agent generates a professional HTML report summarizing findings from other agents.

```python
procurement_report_author_agent = Agent(
    role="Procurement Report Author Agent",
    goal="Create a professional procurement report in HTML format.",
    backstory="Generates a detailed report summarizing product findings.",
    llm=basic_llm,
    verbose=True
)
```

## Running the AI Crew

The AI crew coordinates all agents to complete the procurement process:

```python
rankyx_crew = Crew(
    agents=[
        search_queries_recommendation_agent,
        search_engine_agent,
        scraping_agent,
        procurement_report_author_agent
    ],
    tasks=[
        search_queries_recommendation_task,
        search_engine_task,
        scraping_task,
        procurement_report_author_task
    ],
    process=Process.sequential
)

crew_results = rankyx_crew.kickoff(
    inputs={
        "product_name": "coffee machine for the office",
        "websites_list": ["www.amazon.eg", "www.jumia.com.eg", "www.noon.com/egypt-en"],
        "country_name": "Egypt",
        "no_keywords": 5,
        "language": "English",
        "score_th": 0.10,
        "top_recommendations_no": 3
    }
)
```

## Output

The following outputs are saved in the `./ai-agent-output` directory:

1. **Suggested Search Queries**: JSON file with suggested queries.
2. **Search Results**: JSON file with search results.
3. **Scraped Product Details**: JSON file with product details.
4. **Procurement Report**: HTML file containing the final report.

## Dependencies

Install these packages via `requirements.txt`:

- `agentops`
- `crewai`
- `tavily`
- `scrapegraph_py`
- `pydantic`

## License

MIT License
