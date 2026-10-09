# Multilingual RAG: Ask Questions About Any PDF

A Retrieval-Augmented Generation (RAG) system that answers questions from a PDF, **cites the page numbers**, and says **"not found"** instead of guessing.

This is **Phase 1 of Viva-Mentor** (RAG + voice viva simulator + ML answer scorer).

> **Status:** `english_rag_final.ipynb` is the current English-only version (reliability first).
> The earlier multilingual version (Urdu / Roman Urdu / English, with OCR for scanned Urdu) is kept in `multilingual_version/`.

## What it does

1. Upload a PDF (digital or scanned).
2. Ask a question.
3. Get a short answer with page citations and the source text shown next to it.
4. If the document does not contain the answer, the system says so.

## How it works

```
PDF -> text (OCR for scans) -> chunks -> embeddings -> FAISS index
Question -> retrieve 20 chunks -> rerank to best 5 -> threshold check -> Gemini (strict prompt) -> answer + pages
```

| Step | Tool |
|---|---|
| PDF reading | PyMuPDF, EasyOCR for scanned pages |
| Embeddings | BAAI/bge-m3 |
| Vector search | FAISS |
| Reranking | BAAI/bge-reranker-v2-m3 |
| Answer writing | Gemini API (automatic fallback across models and keys) |
| Interface | Gradio (public link) |
| Platform | Google Colab (free T4 GPU), Google Drive |

**Two safety nets against hallucination**
1. A score threshold: if the best retrieved chunk is too weak, the system answers "not found" without calling the AI.
2. A strict prompt: the AI must use only the retrieved chunks, otherwise reply `NOT_FOUND`.

## Run it

1. Open `english_rag_final.ipynb` in [Google Colab](https://colab.research.google.com) (File > Upload notebook).
2. Runtime > Change runtime type > **T4 GPU**.
3. Add Colab Secrets (key icon): `GEMINI_API_KEY` (and optionally `GEMINI_API_KEY_2`) with Notebook access ON. Get a free key at [aistudio.google.com](https://aistudio.google.com).
4. Put a PDF named `english.pdf` in `MyDrive/viva_mentor_rag/pdfs/`.
5. Runtime > **Run all**. The last cells print a public `.gradio.live` link.

The first run downloads about 4 GB of models. Free API limits and model names change, so check the Gemini docs if a call fails.

## Reusable functions

```python
build_index('/path/to/file.pdf')        # read, chunk, embed, make it the active index
result = ask_rag('Your question')       # {'answer', 'sources', 'top_score'}
```

## Results

**Multilingual version** (own 22-question test set: 16 answerable, 6 unanswerable):

| Setting | Recall@1 | Recall@5 |
|---|---|---|
| FAISS only | 0.88 | 1.00 |
| FAISS + reranker | 1.00 | 1.00 |

Answer accuracy 16/16 and not-found accuracy 6/6 on this set.

**English version:** _add your numbers here after running Cell 10._

These test sets are small and fairly easy. They are a sanity check, not a proof of general accuracy.

## Known limitations

- OCR on scanned text can contain spelling mistakes.
- No table, chart or image understanding.
- Questions needing facts from many pages are harder.
- The free Gemini tier has small daily limits.
- The demo link only works while the Colab notebook is running.
- Only the first 40 pages of an uploaded PDF are read.
- The English version does not support Urdu or Roman Urdu.

## Roadmap

- [ ] Urdu / Roman Urdu support in the main version, with a larger test set
- [ ] Better OCR for scanned documents
- [ ] Hybrid keyword + vector search
- [ ] Local LLM fallback for privacy
- [ ] Permanent hosting
- [ ] Voice viva simulator and answer scorer (Viva-Mentor)

## Privacy note

On the free Gemini tier, prompts may be used by Google to improve its products. Use only non-sensitive documents.

## Team

_Add your names here._
