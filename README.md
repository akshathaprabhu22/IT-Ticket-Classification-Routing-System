# 🎫 IT Ticket Classification & Routing System
### Claude API · Retrieval-Augmented Generation (RAG) · Semantic Classification

---

## 👋 Why I Built This

I'm a Product Manager exploring a question I care deeply about:

> **Which problems actually need AI — and which ones just think they do?**

It's easy to reach for AI as a default. The harder skill is knowing when it genuinely adds value over a simpler solution, and being able to articulate *why* to engineers, stakeholders, and customers.

This project is my hands-on answer to that question. I built an end-to-end AI system from scratch — not to prove I can write code, but to deeply understand the tradeoffs that come up when scoping, building, and evaluating AI features as a PM.

---

## 🤔 Does This Problem Actually Need AI?

This was my first question before writing a single line. Here's the honest analysis:

| Approach | What it can do | Where it breaks down |
|----------|---------------|----------------------|
| **Rule-based routing** (keyword matching) | Fast, cheap, fully auditable | Fails on novel phrasing, slang, ambiguous tickets |
| **Traditional ML** (Decision Tree, SVM) | Good on common categories, no API cost | Brittle on rare categories, no reasoning, no explanation |
| **LLM only** (no retrieval) | Handles novel language well | Can hallucinate categories not in your system |
| **LLM + RAG** ✅ | Grounds decisions in real historical data, explains reasoning | Adds latency and API cost |

**Verdict: AI is genuinely warranted here.** IT tickets are written in natural language, cover edge cases rule-based systems can't anticipate, and incorrect routing has real operational cost. The reasoning and explanation capability of LLMs also matters for trust and compliance — something a decision tree simply can't provide.

---

## 🎯 The Problem

IT support teams at mid-to-large companies receive **thousands of tickets daily**. Manual triage is:
- **Slow** — experienced agents spend time on sorting, not solving
- **Inconsistent** — the same ticket gets routed differently depending on who reads it
- **Costly** — misrouted tickets get bounced between teams, extending resolution time

This system automates triage: given a raw support ticket, it predicts the correct **category**, **priority level**, and **responsible owner** — with a plain-English explanation of *why*.

> 📉 Estimated impact: reduce manual triage time by ~80%, cut misrouting by grounding decisions in historical data.

---

## 🏗️ Architecture

```
New IT Ticket (free text)
        │
        ▼
┌───────────────────┐     ┌──────────────────────────────┐
│  Vectorize text   │────▶│   RAG Index                  │
│  (TF-IDF / BM25)  │     │   (indexed historical tickets)│
└───────────────────┘     └──────────────┬───────────────┘
                                         │  Top-K most similar tickets
                                         ▼
                           ┌──────────────────────────┐
                           │   Claude API             │
                           │   ticket + RAG context   │
                           └─────────────┬────────────┘
                                         │
                    ┌────────────────────▼──────────────────────┐
                    │  category · priority · owner · confidence  │
                    │  + plain-English reasoning                 │
                    └────────────────────────────────────────────┘
```

---

## 📋 What the Notebook Covers

| Step | What happens | PM takeaway |
|------|-------------|-------------|
| **1. Load & Clean** | 27,600 synthetic IT tickets from HuggingFace | Data quality is a product decision — dropping vs. imputing missing labels changes model behaviour |
| **2. Embed** | Claude generates semantic ticket signatures | Embeddings capture meaning, not just keywords — critical for handling natural language variation |
| **3. Index** | Build TF-IDF / BM25 retrieval index | Retrieval quality directly caps routing accuracy — this is where most production systems underinvest |
| **4. Route** | Claude + RAG context → category, priority, owner | Confidence scores let you decide when to escalate to a human — key for trust and liability |
| **5. Compare** | RAG vs. LLM-only vs. Decision Tree | Benchmarking is how you justify the added cost and complexity of AI to stakeholders |
| **6. Optimize** | Batch API, sub-agents, caching | Token costs at scale are a real product concern — AI features need a cost model, not just an accuracy metric |
| **7. Evaluate** | Accuracy, resolution time, escalation rate | Defining the right success metric is the PM's job — accuracy alone is rarely the full picture |

---

## 💡 Key PM Learnings

**1. RAG vs. fine-tuning is a product decision, not just a technical one.**
Fine-tuning means retraining the AI every time your domain changes — expensive and slow. RAG skips that entirely: you just update the reference database and the model stays the same. For an IT helpdesk where new tools get added and ticket types evolve constantly, RAG is simply easier to maintain. It's the difference between retraining an employee from scratch versus handing them an updated manual.

**2. Confidence scores are a feature, not an afterthought.**
The routing model returns a confidence score with every prediction. Tickets below 70% confidence get flagged for human review. This is how you build a system that scales *without* sacrificing accuracy — a core PM tradeoff between automation and oversight.

**3. The baseline matters.**
A decision tree on the same data achieves strong accuracy on the dominant `SOFTWARE` category — because 70%+ of tickets fall there. An AI system that doesn't beat the baseline on *hard cases* isn't worth its cost. Always define what "better" means before you build.

**4. Cost is a product metric.**
At 27k tickets/day, even a 1¢ per-call API cost becomes significant. The notebook includes token batching and caching strategies — because shipping an AI feature without a cost model is like shipping a product without a pricing strategy.

---

## 🛠️ Tech Stack

| Layer | Tools |
|-------|-------|
| LLM | Anthropic Claude API |
| Data | HuggingFace Datasets · Pandas · NumPy |
| Retrieval | TF-IDF · BM25 · Cosine Similarity |
| ML Baseline | Scikit-learn Decision Tree |
| Environment | Python 3.12 · Google Colab |

---

## ⚙️ Setup

```bash
pip install anthropic datasets pandas numpy scikit-learn rank_bm25
```

Set your Anthropic API key:

```bash
export ANTHROPIC_API_KEY=your_api_key_here
```

Open `ticket_routing_llm_improved.ipynb` in Jupyter or Colab and run all cells.

---

## 📁 Dataset

- **Source**: [`KameronB/synthetic-it-callcenter-tickets`](https://huggingface.co/datasets/KameronB/synthetic-it-callcenter-tickets)
- **Size**: 27,600 synthetic IT support tickets
- **Categories**: SOFTWARE · ACCOUNT · PIV CARD · EMAIL · SECURITY · PRINTER · NETWORK · HARDWARE

---

## 🔗 Author

**Akshatha Prabhu** — PM learning to evaluate AI honestly · [GitHub](https://github.com/akshathaprabhu22)
