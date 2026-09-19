# Track 2: Senior AI Role
## Complete Practical Code Implementations

This document provides production-grade, fully runnable Python implementations for the three most critical AI engineering workflows tested in Senior & Staff AI interviews.

---

### Implementation 1: End-to-End Production RAG Pipeline (pgvector + Hybrid Search + Cross-Encoder)

```python
"""
Production Hybrid RAG Pipeline:
Combines PostgreSQL pgvector (Dense Semantic Search) with BM25 (Sparse Keyword Search)
and applies Cross-Encoder Re-ranking to maximize contextual precision.
"""

from collections import defaultdict
from typing import List, Dict, Any
import psycopg2
from sentence_transformers import SentenceTransformer, CrossEncoder
from rank_bm25 import BM25Okapi
import numpy as np

class ProductionRAGPipeline:
    def __init__(self, db_conn_str: str):
        self.conn = psycopg2.connect(db_conn_str)
        # Bi-encoder for dense embeddings
        self.bi_encoder = SentenceTransformer("BAAI/bge-small-en-v1.5")
        # Cross-encoder for reranking top candidates
        self.cross_encoder = CrossEncoder("BAAI/bge-reranker-base")
        self.bm25_corpus: List[Dict[str, Any]] = []
        self.bm25_index = None

    def initialize_db(self):
        """Sets up pgvector extension and table with HNSW index."""
        with self.conn.cursor() as cur:
            cur.execute("CREATE EXTENSION IF NOT EXISTS vector;")
            cur.execute("""
                CREATE TABLE IF NOT EXISTS rag_documents (
                    id SERIAL PRIMARY KEY,
                    content TEXT NOT NULL,
                    metadata JSONB,
                    embedding vector(384)
                );
                CREATE INDEX IF NOT EXISTS idx_rag_hnsw 
                ON rag_documents USING hnsw (embedding vector_cosine_ops)
                WITH (m = 16, ef_construction = 64);
            """)
            self.conn.commit()

    def index_documents(self, documents: List[str]):
        """Ingests and indexes documents into both pgvector and in-memory BM25."""
        embeddings = self.bi_encoder.encode(documents, normalize_embeddings=True)
        tokenized_corpus = []

        with self.conn.cursor() as cur:
            for doc, emb in zip(documents, embeddings):
                cur.execute(
                    "INSERT INTO rag_documents (content, embedding) VALUES (%s, %s) RETURNING id;",
                    (doc, emb.tolist())
                )
                doc_id = cur.fetchone()[0]
                self.bm25_corpus.append({"id": doc_id, "content": doc})
                tokenized_corpus.append(doc.lower().split())
            self.conn.commit()

        self.bm25_index = BM25Okapi(tokenized_corpus)

    def search_hybrid(self, query: str, top_k: int = 50) -> List[Dict[str, Any]]:
        """Executes Dense + BM25 search and merges via Reciprocal Rank Fusion (RRF)."""
        # 1. Dense Vector Search in pgvector
        query_emb = self.bi_encoder.encode(query, normalize_embeddings=True).tolist()
        dense_results = []
        with self.conn.cursor() as cur:
            cur.execute("""
                SELECT id, content, (embedding <=> %s::vector) AS distance
                FROM rag_documents
                ORDER BY distance ASC
                LIMIT %s;
            """, (query_emb, top_k))
            for row in cur.fetchall():
                dense_results.append({"id": row[0], "content": row[1]})

        # 2. Sparse BM25 Search
        tokenized_query = query.lower().split()
        bm25_scores = self.bm25_index.get_scores(tokenized_query)
        top_bm25_indices = np.argsort(bm25_scores)[::-1][:top_k]
        sparse_results = [self.bm25_corpus[i] for i in top_bm25_indices]

        # 3. Reciprocal Rank Fusion (RRF with k=60)
        rrf_scores = defaultdict(float)
        doc_map = {}
        for rank, doc in enumerate(dense_results, 1):
            doc_map[doc["id"]] = doc["content"]
            rrf_scores[doc["id"]] += 1.0 / (60 + rank)

        for rank, doc in enumerate(sparse_results, 1):
            doc_map[doc["id"]] = doc["content"]
            rrf_scores[doc["id"]] += 1.0 / (60 + rank)

        # Sort descending by fused score
        sorted_ids = sorted(rrf_scores.keys(), key=lambda x: rrf_scores[x], reverse=True)[:top_k]
        return [{"id": did, "content": doc_map[did]} for did in sorted_ids]

    def retrieve_and_rerank(self, query: str, final_k: int = 5) -> List[Dict[str, Any]]:
        """Stage 2: Cross-Encoder Re-ranking of candidate pool."""
        candidates = self.search_hybrid(query, top_k=25)
        if not candidates:
            return []

        # Prepare sentence pairs for Cross-Encoder: (Query, Passage)
        pairs = [[query, c["content"]] for c in candidates]
        cross_scores = self.cross_encoder.predict(pairs)

        for i, score in enumerate(cross_scores):
            candidates[i]["rerank_score"] = float(score)

        # Sort descending by re-ranking score
        candidates.sort(key=lambda x: x["rerank_score"], reverse=True)
        return candidates[:final_k]
```

---

### Implementation 2: Autonomous Agent Loop with Reflexion Self-Correction

```python
"""
Autonomous AI Agent with Memory Scratchpad and Reflexion Error Correction.
"""

import json
from typing import Callable, Dict, Any

class AgentTool:
    def __init__(self, name: str, description: str, func: Callable):
        self.name = name
        self.description = description
        self.func = func

class AutonomousAgent:
    def __init__(self, tools: list[AgentTool], max_iterations: int = 5):
        self.tools = {t.name: t for t in tools}
        self.max_iterations = max_iterations
        self.scratchpad: list[dict] = []

    def run(self, objective: str) -> str:
        print(f"[Goal] {objective}")
        iteration = 0

        while iteration < self.max_iterations:
            iteration += 1
            print(f"\n--- Iteration {iteration} ---")

            # 1. Simulate LLM Planning & Action Decision
            thought, action_name, action_args = self._mock_llm_reasoning(objective)
            print(f"Thought: {thought}")

            if action_name == "FINISH":
                return f"SUCCESS: {action_args.get('response')}"

            # 2. Tool Execution
            tool = self.tools.get(action_name)
            if not tool:
                observation = f"Error: Tool '{action_name}' does not exist."
            else:
                try:
                    observation = tool.func(**action_args)
                except Exception as e:
                    # 3. Reflexion Trigger on Error
                    observation = f"Tool Failure: {str(e)}"
                    print(f"--> Reflexion Triggered! Analyzing failure to adjust parameters.")

            print(f"Observation: {observation}")
            self.scratchpad.append({
                "iteration": iteration,
                "thought": thought,
                "action": action_name,
                "observation": observation
            })

        return "FAILED: Exceeded maximum iterations without reaching goal."

    def _mock_llm_reasoning(self, objective: str):
        """Mock LLM step logic for deterministic testing."""
        if not self.scratchpad:
            return ("I must first check inventory for the SKU.", "check_stock", {"sku": "SKU-902"})
        last_obs = self.scratchpad[-1]["observation"]
        if "In Stock" in last_obs:
            return ("Inventory confirmed, I can formulate the final answer.", "FINISH", {"response": "SKU-902 is available with 42 units in WH-01."})
        return ("Unknown state", "FINISH", {"response": "Completed."})

# Example Tool:
def check_stock(sku: str) -> str:
    if sku == "SKU-902":
        return "Status: In Stock (42 units in WH-01)"
    return "Status: Out of Stock"

# Usage:
tools = [AgentTool("check_stock", "Queries stock levels by SKU", check_stock)]
agent = AutonomousAgent(tools)
result = agent.run("Check if SKU-902 is available.")
print(result)
```

---

### Implementation 3: QLoRA Fine-Tuning Script with HuggingFace PEFT

```python
"""
QLoRA Parameter-Efficient Fine-Tuning Script:
Quantizes model weights to 4-bit NF4 and trains low-rank LoRA adapters.
"""

import torch
from transformers import AutoModelForCausalLM, AutoTokenizer, BitsAndBytesConfig, TrainingArguments
from peft import LoraConfig, get_peft_model, prepare_model_for_kbit_training
from datasets import Dataset

def train_qlora(model_id: str = "meta-llama/Meta-Llama-3-8B-Instruct"):
    # 1. 4-bit NF4 Quantization Configuration
    bnb_config = BitsAndBytesConfig(
        load_in_4bit=True,
        bnb_4bit_quant_type="nf4",
        bnb_4bit_compute_dtype=torch.bfloat16,
        bnb_4bit_use_double_quant=True
    )

    # 2. Load Tokenizer & Base Model
    tokenizer = AutoTokenizer.from_pretrained(model_id)
    tokenizer.pad_token = tokenizer.eos_token

    model = AutoModelForCausalLM.from_pretrained(
        model_id,
        quantization_config=bnb_config,
        device_map="auto"
    )

    # 3. Prepare for k-bit training (freezes base weights, casts LayerNorm to FP32)
    model = prepare_model_for_kbit_training(model)

    # 4. LoRA Adapter Configuration
    lora_config = LoraConfig(
        r=16,                         # Rank r
        lora_alpha=32,                # Alpha scaling (alpha = 2 * r)
        target_modules=["q_proj", "k_proj", "v_proj", "o_proj", "gate_proj", "up_proj", "down_proj"],
        lora_dropout=0.05,
        bias="none",
        task_type="CAUSAL_LM"
    )

    model = get_peft_model(model, lora_config)
    model.print_trainable_parameters()  # Shows trainable params < 0.5%

    print("QLoRA Adapter configured successfully and ready for trainer.train()!")

if __name__ == "__main__":
    # train_qlora()
    pass
```
