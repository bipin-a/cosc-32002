# **NLP Assignment: Local Retrieval-Augmented Generation (RAG) System**

## **Objective**

Build a **local Retrieval-Augmented Generation (RAG) system** that lets users upload PDFs or URLs, processes their content, and answers questions about it using **pretrained models from Hugging Face** for embeddings and response generation. You’ll use **Docker Compose** to orchestrate a **Streamlit frontend**, **FastAPI backend**, and **FAISS vector database**. The system will retrieve relevant text from documents and generate answers, running entirely on your computer—no cloud deployment needed. Using the **Haystack framework** is optional but recommended to simplify setup.

> **NOTE**: It’s okay if your system doesn’t give perfect answers every time. As discussed in class, explain in your video if it struggles, why it might be happening, and the pros and cons of your approach. You’re graded on understanding and reflection, not flawless results!
>
> **Reminder**: You do **not** need to deploy this RAG on the cloud. Get it working locally!

---

## **Functional Requirements**

Your RAG system will process PDFs or URLs, store their content as vectors in FAISS using a Hugging Face model for embeddings, and answer user questions by retrieving text and generating responses with another Hugging Face model. Here’s what to build:

### 1. **Document Input**
- Use **Streamlit** to let users:
  - Upload a PDF file (`st.file_uploader`).
  - Enter a URL (e.g., a Wikipedia page) in a text box (`st.text_input`).
- Send the PDF or URL to the **FastAPI backend** for processing.
- Support at least one PDF (up to 10 pages) and one URL per session.
- Save uploaded files temporarily in a local folder (e.g., `./uploads`).

> **What’s this do?** It’s like giving your app a book or webpage to study before it answers questions.

---

### 2. **Document Processing**
- In the **FastAPI backend**, process inputs:
  - For PDFs: Use `PyPDF2` or `pdfplumber` to extract text (simple libraries for reading PDFs).
  - For URLs: Use `requests` and `beautifulsoup4` to scrape text (skip images or ads, focus on main content like article text).
- **Chunk** the text into smaller pieces (e.g., 200–500 words each) for easier searching.
- Keep it simple: Remove extra spaces or weird characters (e.g., use `.strip()` or regex).
- (Optional) Preprocess text (e.g., lowercase or remove stopwords) if it improves results—explain your choice in the video.

> **Why chunk?** Big documents are hard to search. It’s like splitting a book into chapters to find the right page quickly.

---

### 3. **Embedding Generation**
- Use a **Hugging Face pretrained model** to turn text chunks into **vectors** (number lists capturing meaning).
  - Recommended: `sentence-transformers/all-MiniLM-L6-v2` (~80MB, fast and lightweight).
  - Load with `transformers` or `sentence-transformers` library (e.g., `SentenceTransformer("all-MiniLM-L6-v2")`).
- (Optional) If using **Haystack**, configure it to use this model (see Haystack’s docs).
- Store vectors in **FAISS**, a lightweight local vector database:
  - Use `faiss.IndexFlatL2` for simple similarity search (compares vectors with Euclidean distance).
  - Save the index to a local file (e.g., `./faiss_index`) for persistence.
- Store each chunk’s text alongside its vector (e.g., in a Python list or dictionary) for retrieval.

> **What’s a vector?** Think of it as a GPS coordinate for a sentence—it helps the app find ideas “close” to your question.

---

### 4. **Vector Search and Response Generation**
- Let users type a question in Streamlit (e.g., “What’s the main idea of this PDF?”).
- In the FastAPI backend:
  - Convert the question to a vector using the same **Hugging Face embedding model**.
  - Search **FAISS** for the top 3–5 most similar text chunks (use L2 distance for simplicity).
  - Send these chunks plus the question to a **Hugging Face generation model**.
  - Recommended: `t5-small` (~240MB, good for question-answering or summarization).
  - Load with `transformers` (e.g., `pipeline("text2text-generation", model="t5-small")`).
- Generate a natural-language answer based on the question and retrieved chunks.
- In Streamlit, show:
  - The question (e.g., “Q: What’s this about?”).
  - The generated answer (e.g., “A: It’s about AI advancements in 2023.”).
- (Optional) Show the top retrieved chunk for transparency (e.g., “Based on: [chunk text]”).

> **How’s this work?** It’s like asking a librarian to find book pages close to your question, then having a smart friend summarize them in plain English.

---

### 5. **Streamlit Frontend**
- Build a simple interface with:
  - **Upload section**: Buttons for PDF upload and URL input.
  - **Question box**: Text area for typing questions (`st.text_area`).
  - **Results**: Show the generated answers clearly (use `st.markdown` for formatting).
  - **Feedback**: Display “Processing…” or errors (e.g., “Invalid URL”).
- Keep it clean:
  - Use `st.columns` or tabs (e.g., “Upload” tab, “Ask” tab).
  - Add clear labels (e.g., “Drop your PDF here”).
- Show at least one question and answer at a time, with a button to ask another.
- (Optional) Add a history of questions/answers in a sidebar.

> **Why Streamlit?** It’s a quick way to make a web page where users can upload files and ask questions without coding HTML.

---

### 6. **FastAPI Backend**
- Create endpoints to handle:
  - `/upload`: Process PDFs or URLs, chunk text, generate embeddings with the Hugging Face model, and store vectors in FAISS.
  - `/query`: Take a question, generate its embedding, search FAISS, call the Hugging Face generation model, and return the answer.
- Use JSON to communicate with Streamlit (e.g., send `{ "answer": "..." }`).
- Handle errors (e.g., return `{ "error": "Bad PDF" }` if parsing fails).
- Process one file or question at a time to avoid memory issues.

> **Why FastAPI?** It’s the engine—takes Streamlit’s requests and handles processing, searching, and answering.

---

### 7. **Docker Compose Setup (Deployment to Cloud Not Required)**
- Use **Docker Compose** to run two services:
  - **Streamlit**: Frontend (port 8501).
  - **FastAPI**: Backend with FAISS and Hugging Face models (port 8000).
- Write a `docker-compose.yml` to:
  - Set up services with official images (e.g., `python:3.9-slim` for both).
  - Mount local folders for persistence (e.g., `./uploads`, `./faiss_index`).
  - Ensure FastAPI has enough memory for model loading (e.g., set `mem_limit: 4g` if needed).
- Create a `Dockerfile` for each:
  - Install dependencies (e.g., `streamlit`, `fastapi`, `sentence-transformers`, `transformers`, `faiss-cpu`, `PyPDF2`, `torch`).
  - Keep it light—use `python:3.9-slim` as the base.
  - Pre-download models in the Dockerfile to avoid runtime delays (e.g., `RUN python -c "from sentence_transformers import SentenceTransformer; SentenceTransformer('all-MiniLM-L6-v2')"`).
- Test locally with `docker-compose up`.

**Docker Compose Resources**:
- [Official Docker Compose Docs](https://docs.docker.com/compose/) – Basics of `docker-compose.yml`.
- [Docker Compose for Python](https://docs.docker.com/compose/gettingstarted/) – Simple Python example.
- [FAISS Installation Guide](https://github.com/facebookresearch/faiss/wiki/Getting-started) – How to set up FAISS.
- [Hugging Face Transformers](https://huggingface.co/docs/transformers/installation) – Setup guide.

> **Why Docker?** It’s like a lunchbox—keeps Streamlit and FastAPI packed and ready to run.

---

### 8. **Haystack Option (Optional)**
- Instead of building manually, you can use **Haystack** to simplify:
  - Use Haystack’s `FAISSDocumentStore` for vector storage.
  - Configure Haystack to use `all-MiniLM-L6-v2` for embeddings and `t5-small` for generation.
  - Check the [Haystack Quickstart](https://docs.haystack.deepset.ai/docs/get-started).
- If you skip Haystack, explain why in your video (e.g., “I wanted to learn how transformers work”).
- Haystack saves time but hides some details—both are fine!

> **Why Haystack?** It’s like a prebuilt RAG kit—faster to set up but less control.

---

### 9. **Visualization**
- Show in Streamlit:
  - Upload status (e.g., “PDF loaded: 5 pages”).
  - Question and generated answer (e.g., “Q: What’s this? A: It’s about AI.”).
  - (Optional) Top retrieved chunk or similarity score (e.g., “Based on text with 85% match”).
- Keep it clear:
  - Use colors (e.g., green for success, red for errors).
  - Space elements (e.g., `st.markdown("---")` for dividers).
- (Optional) Add a loading spinner during processing (`st.spinner`).

---

## **Demonstration Video Requirements**

Record a **5–10 minute** video showing your RAG system. Focus on:
- **App walkthrough**:
  - Upload a PDF or URL in Streamlit.
  - Ask 2–3 questions (e.g., “What’s the main topic?” or “What’s mentioned about AI?”).
  - Show the generated answers and (if included) retrieved chunks.
- **System and algorithms**:
  - Explain in simple terms:
    - “I use a Hugging Face model to turn text into vectors for searching.”
    - “FAISS finds chunks closest to my question’s vector.”
    - “Another Hugging Face model writes a clear answer from those chunks.”
  - Describe choices (e.g., “I picked `t5-small` because it’s fast” or “Haystack saved me time”).
  - Pros and cons (e.g., “`t5-small` is quick but sometimes too brief”).
  - If answers are off, say why (e.g., “My chunks were too short, so it missed context”).
- **System overview**:
  - How parts connect (e.g., “Streamlit sends questions to FastAPI, which uses FAISS and Hugging Face models”).
  - Role of Docker (e.g., “It runs everything locally in containers”).
- **Insights**:
  - What you learned (e.g., “Hugging Face models make NLP so accessible!”).
  - Limits (e.g., “PDFs with tables confused my parser”).
  - How you’d improve (e.g., “I’d try a bigger model or smaller chunks”).
- Be **precise**—no rambling. Plan your points but don’t read a script. Talk like you’re explaining to a classmate who knows Python but not NLP.
- **Tip**: Rehearse once to avoid “um” or repetition. Keep it clear and engaging.

---

## **Testing Instructions**

Test your system with:
1. **PDF**: A short document (e.g., a 5-page research paper or manual).
   - Source: Try [arXiv](https://arxiv.org/) for open-access PDFs.
2. **URL**: A simple webpage (e.g., a Wikipedia article like [“Natural Language Processing”](https://en.wikipedia.org/wiki/Natural_language_processing)).
Test with these questions:
- Broad: “What’s this document about?”
- Specific: “What’s mentioned about machine learning?”
- Tricky: “What’s not in the text?” (to check if the model sticks to the document).
Check:
- PDF/URL processes correctly.
- Answers are relevant (even if not perfect).
- Docker services (Streamlit, FastAPI) run without crashing.

---

## **Submission Requirements**
- Source code (Streamlit frontend, FastAPI backend).
- `docker-compose.yml` and `Dockerfile`(s).
- Sample PDF or URL used for testing (or a link to it).
- Demonstration video (5–10 minutes).
- **Reminder**: You do **not** need to deploy this RAG on the cloud. Get it working locally!

---

## **Support**
- **Stuck?** DM me on Teams—I reply fast!
- **Confused?** Ask about FAISS, Haystack, Hugging Face, or embeddings in Teams.