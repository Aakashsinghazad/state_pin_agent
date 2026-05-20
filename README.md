# RuPay Transaction Data Cleansing Pipeline 🚀

An enterprise-grade, high-performance, parallel data-cleansing framework built with **LangGraph**, **LangChain**, and **OpenAI**. 

This repository implements a **Hybrid Gatekeeper & Local Lookup Architecture** designed to clean, standardize, and enrich large volumes of transaction location data (20,000+ rows) while maximizing speed and keeping LLM token costs close to **$0**.

---

## 🏗️ Architecture Overview

The pipeline leverages an advanced parallel processing (Fan-Out/Fan-In) graph structure that splits data-cleansing responsibilities across two highly optimized parallel agent nodes before combining them cleanly side-by-side:

1. **Gatekeeper Hybrid Text Agent (Node 1):**
   - Uses an in-memory `POPULAR_CITIES_SET` and a dynamic `LEARNED_CORRECTION_CACHE` to check city names locally in a fraction of a millisecond.
   - If a city is valid or has been corrected before (e.g., `"Delhi"`, or `"Mum bai"` seen twice), it completely **bypasses the LLM** ($0 cost).
   - Only genuinely new anomalies are batched and processed concurrently using LangChain's high-speed `.abatch()` engine.

2. **Lightning-Fast Local PIN Code Agent (Node 2):**
   - Eliminates unpredictable and slow internet API calls.
   - Loads the official postal directory into local RAM to match postal codes instantly to their official `regionname` (City) and `statename` (State).

3. **Merge Results Node (Node 3):**
   - Leverages a custom LangGraph dictionary reducer function to prevent parallel write race conditions.
   - Automatically aligns the outputs vertically row-by-row into a side-by-side comparison format.

---

## 🛠️ Prerequisites & Installation

Anyone with basic Python skills can run this project locally, in **Jupyter Notebook**, or **Google Colab**.

First, install the required packages in your terminal or a notebook cell:

```bash
pip install pandas langgraph langchain langchain-openai httpx pydantic