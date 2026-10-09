# Multilingual RAG: Ask Questions About Any PDF

What it is
A system that answers questions about any PDF. The user uploads a PDF, asks a question in English, and gets a short answer with page numbers and the source text. If the document does not contain the answer, the system says “Not found in the document” instead of guessing.

How it works

It reads the PDF page by page. Scanned pages are read with OCR (text recognition from images).
It cuts the text into small pieces of about 600 characters and keeps the page number for each piece.
It turns each piece into numbers that represent its meaning, and stores them in a fast search index (FAISS).
When a question is asked, it finds the 20 most related pieces, then a second model (a reranker) picks the best 5.
If the best piece is not relevant enough, the system answers “not found” without calling the AI.
Otherwise, the Gemini AI writes the answer using only those 5 pieces and cites the pages.
The user sees the answer and the source text in a web page (Gradio) with a public link.

Tools used
PyMuPDF, EasyOCR, bge-m3 (embeddings), FAISS, bge-reranker-v2-m3, Gemini API with automatic fallback across models and keys, Gradio, Google Colab (free T4 GPU), Google Drive.

Why it is useful

Answers can be checked, because every answer shows its page and source text.
It admits when it does not know, which matters for study, law, and business documents.
It works on any new PDF immediately, with no training.
It runs on free tools.

How it differs from general AI chat
General chat answers from what it learned in training, so it can invent answers and gives no page references. Our system answers only from the uploaded document, shows its sources, refuses when the answer is missing, and was measured with our own tests.

Safety against wrong answers

A score threshold stops weak matches before the AI is called.
A strict prompt forces the AI to use only the retrieved text, or reply “not found”.

Final results (honest version)
The numbers we measured come from the earlier multilingual version, on our own 22-question test set (16 answerable, 6 unanswerable):

Retrieval, FAISS only: the right page was ranked first for 88% of questions.
Retrieval, FAISS plus reranker: the right page was ranked first for 100%.
Answer accuracy: 16 out of 16.
Correct “not found” on unanswerable questions: 6 out of 6.

The final English-only notebook has not been measured yet. Run Cell 10 and add your own numbers before presenting them.

Limits 

The test set is small and fairly easy, so it is a sanity check, not a proof of general accuracy.
Scanned pages depend on OCR, which can make spelling mistakes.
It does not understand tables, charts, or images.
Questions needing facts from many pages are harder.
The free Gemini tier has small daily limits, which is why there is a fallback chain.
The demo link works only while the Colab notebook is running.
Only the first 40 pages of an uploaded PDF are read.
This version supports English only. Urdu and Roman Urdu support was built and tested earlier, and is planned as future work.

Future work
Urdu and Roman Urdu support, better OCR, keyword plus meaning hybrid search, a larger test set, a local AI model for privacy, and permanent hosting.

One-sentence description
“It lets you ask questions about any PDF and get answers with page numbers, and it says ‘not found’ instead of guessing.”
## Privacy note
On the free Gemini tier, prompts may be used by Google to improve its products. Use only non-sensitive documents.
## Team
M.Ijlal
