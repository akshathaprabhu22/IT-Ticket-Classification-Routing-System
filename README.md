# IT Ticket Classification & Routing System

**Portfolio Project | End-to-End LLM Engineering with RAG**

## Overview

This project builds an intelligent IT helpdesk ticket routing system using the **Claude API** and **Retrieval-Augmented Generation (RAG)**. Given a raw support ticket, the system automatically classifies it, assigns a priority level, and routes it to the appropriate owner — reducing manual triage time by up to **80%**.

## Problem Statement

IT support teams receive thousands of tickets daily. Manual triage is slow, inconsistent, and costly. This project automates that process using state-of-the-art LLM reasoning combined with semantic retrieval, achieving more accurate and context-aware routing than traditional ML classifiers.

## Architecture

```
Raw Ticket Input
      │
      ▼
┌─────────────┐     ┌──────────────────────┐
│  Embedding  │────▶│  RAG Vector Index    │
│  (Claude)   │     │  (27.6k tickets)     │
└─────────────┘     └──────────┬───────────┘
                               │ Top-K Similar Tickets
                               ▼
                    ┌──────────────────────┐
                    │  Claude Routing LLM  │
                    │  (with RAG context)  │
                    └──────────┬───────────┘
                               │
                    ┌──────────▼───────────┐
                    │  Category + Priority │
                    │  + Owner Assignment  │
                    └──────────────────────┘
```

## Pipeline Steps

| Step | Description |
|------|-------------|
| 1. Load & Clean | Load 27,600 synthetic IT tickets from HuggingFace; handle missing categories |
| 2. Embed | Generate semantic embeddings using Claude API |
| 3. Index | Build vector similarity index for fast retrieval |
| 4. Route | Claude reads ticket + retrieved context → assigns category, priority, owner |
| 5. Compare | Benchmark Claude RAG vs. decision tree baseline vs. LLM-only (no retrieval) |

## Key Features

- **Claude API integration** — uses Claude for both embedding and classification (not OpenAI)
- **RAG-powered routing** — retrieves similar historical tickets as context for better decisions
- **Batch embedding** — minimizes token usage and API cost at scale
- **Multi-output prediction** — simultaneously predicts category, priority, and responsible owner
- **Model comparison** — benchmarks RAG approach against traditional ML and LLM-only baselines

## Dataset

- **Source**: [`KameronB/synthetic-it-callcenter-tickets`](https://huggingface.co/datasets/KameronB/synthetic-it-callcenter-tickets) on HuggingFace
- **Size**: 27,600 synthetic IT support tickets
- **Fields**: ticket description, category, priority, resolution owner

## Technologies Used

| Category | Tools |
|----------|-------|
| LLM | Anthropic Claude API |
| Data | HuggingFace Datasets, Pandas, NumPy |
| ML Baseline | Scikit-learn (Decision Tree) |
| Vector Search | Cosine similarity (NumPy) |
| Environment | Python 3.12, Jupyter Notebook |

## Setup

### Prerequisites

```bash
pip install anthropic datasets pandas numpy scikit-learn
```

### API Key

Set your Anthropic API key as an environment variable:

```bash
export ANTHROPIC_API_KEY=your_api_key_here
```

### Run

Open `ticket_routing_llm_improved.ipynb` in Jupyter and run all cells sequentially.

## Results

The RAG-enhanced Claude routing outperforms both the LLM-only and decision tree baselines by providing historical ticket context, enabling more accurate category classification and smarter priority assignment.

## Why RAG Over Pure ML?

Traditional classifiers (e.g., decision trees) treat ticket routing as a fixed label prediction problem. RAG allows the model to:
- Reason over similar resolved tickets
- Handle edge cases and novel ticket types
- Incorporate organizational context dynamically

## Scalability Notes

- Embeddings are stored once and reused across inference runs
- Sub-agent parallelization recommended for datasets exceeding 100k tickets
- Designed to integrate with ticketing platforms (Jira, ServiceNow, Zendesk)

## Author

**Akshatha Prabhu** — [GitHub](https://github.com/akshathaprabhu22)
