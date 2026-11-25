# Local medical RAG with Ollama

Two Jupyter notebooks. Everything is free and runs locally (Ollama, sentence-transformers, bm25s).

| File | What it does |
|---|---|
| `medical_rag_ollama.ipynb` | RAG over your textbook chunks (`medrag_chunks.jsonl`). Evaluates retrieval (Hit@k, Precision, Recall, MRR, nDCG), answer quality (correctness with two judges and a manual audit, F1, ROUGE, BLEU, semantic similarity, faithfulness, hallucination, relevancy), MedQA accuracy (closed-book vs RAG, paired test), latency. Optional dev-set experiments to improve MedQA. |
| `ddxplus_kg_rag.ipynb` | Simplified re-implementation of the knowledge-graph RAG idea from arXiv:2502.04413 on the public DDXPlus dataset, plus two changes of our own (hybrid case retrieval, textbook-verified KG features), tested one at a time. |
| `requirements.txt` | `pip install -r requirements.txt` (the notebooks also install what they need). |

## Setup (Windows)
1. Install Python 3.10+ and [Ollama](https://ollama.com/download).
2. `pip install -r requirements.txt`
3. Make sure the Ollama server is running (`ollama serve`, or open the Ollama app), then `ollama pull qwen3:4b`.
   The first notebook also has a cell that starts the server and downloads the model for you.
4. Put `medrag_chunks.jsonl` next to the notebooks.
5. For the DDXPlus notebook: download the **English** DDXPlus files (link in https://github.com/mila-iqia/ddxplus) into a folder `ddxplus/`:
   `release_evidences.json`, `release_train_patients.*`, `release_test_patients.*`.

## Running
- Run cells **top to bottom**. After a kernel restart, re-run the earlier cells first (otherwise you get `NameError`).
- Slow steps show progress bars. Embeddings, synthetic questions, KG features and DDXPlus predictions are cached (`cache/`, `cache_ddx/`), so re-runs are fast and interrupted runs resume.
- If you change settings that affect prompts or retrieval, delete the matching cache files.
- Results are saved to `results/` and `results_ddx/` (CSV, JSON, PNG).

## Measured in the original run (qwen3:4b, 16 GB RAM, CPU), with caveats
| Measure | Result | Note |
|---|---|---|
| Retrieval Hit@5, hybrid + rerank | 0.887 | 150 LLM-generated questions; easier than real exam questions |
| Answer correctness (audited) | 0.95-0.975 | 40 generated questions; judge v1 gave 0.70 and was too strict; audit done by one reader |
| MedQA-USMLE accuracy, closed-book | 0.53 (95% CI 0.43-0.63) | 100 questions |
| MedQA-USMLE accuracy, RAG | 0.60 (95% CI 0.50-0.69) | gain over closed-book not clearly distinguishable from chance at n=100 |

Do not describe these as "85-88% accuracy on medical QA". The MedQA numbers are the real exam benchmark and are far below that with a 4B model.

## Honest-reporting checklist
- Report confidence intervals and sample sizes. Report every judge version and how the audit was done.
- Tune on a dev set, run the final setup once on the test set.
- The DDXPlus notebook is a simplified re-implementation: only L3 accuracy is comparable with the paper, and only roughly. Cite the paper, write your own text, and check novelty before claiming it.
- DDXPlus is synthetic and for research use only. Nothing here is medical advice.
