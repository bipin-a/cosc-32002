# **NLP Assignment: Local Retrieval-Augmented Generation (RAG) System**

## **Objective**

Build a **local Retrieval-Augmented Generation (RAG) system** that lets users upload PDFs or URLs, processes their content, and answers questions about it using text embeddings and similarity search. You’ll use **Docker Compose** to orchestrate a **Streamlit frontend**, **FastAPI backend**, and **FAISS vector database** (no LLM required). The system should retrieve relevant text from documents to answer queries, running entirely on your computer—no cloud deployment needed. Using the **Haystack framework** is optional but recommended to simplify setup.

> **NOTE**: It’s okay if your system doesn’t retrieve perfect answers every time. As discussed in class, explain in your video if it struggles, why it might be happening, and the pros and cons of your approach. You’re graded on understanding, and reflection, not flawless results!


**Reminder**: You do **not** need to deploy this RAG on the cloud. But get it working locally!

---

## **Functional Requirements**

Your RAG system will process PDFs or URLs, store their content as vectors in FAISS, and answer user questions by finding the most relevant text chunks. No language model is needed—just focus on retrieving accurate text. Here’s what to build:

### 1. **Document Input**
- Use **Streamlit** to let users:
  - Upload a PDF file (`st.file_uploader`).
  - Enter a URL (e.g., a Wikipedia page) in a text box (`st.text_input`).
- Send the PDF or URL to the **FastAPI backend** for processing.
- Support at least one PDF (up to 10 pages) and one URL per session.
- Save uploaded files temporarily in a local folder (e.g., `./uploads`).

> **What’s this do?** It’s like giving your app a book or webpage to read before it picks out key passages.

---

### 2. **Document Processing**
- In the **FastAPI backend**, process inputs:
  - For PDFs: Use `PyPDF2` or `pdfplumber` to extract text (simple libraries for reading PDFs).
  - For URLs: Use `requests` and `beautifulsoup4` to scrape text (skip images or ads, focus on main content like article bodies).
- **Chunk** the text into smaller pieces (e.g., 200–500 words each) to make searching easier.
- Keep it simple: Remove extra spaces or weird characters (e.g., use `.strip()` or regex).
- (Optional) Preprocess text (e.g., lowercase or remove stopwords) if it improves search—explain your choice in the video.

> **Why chunk?** Big documents are tough to search. Chunking is like tearing a book into chapters so you can find the right page fast.

---

### 3. **Embedding Generation**
- Turn text chunks into **vectors** (number lists capturing meaning) using an embedding model.
- Options:
  - Use **sentence-transformers** (e.g., `all-MiniLM-L6-v2`, a small, fast model).
  - Or use **Haystack**’s built-in embedders if you choose it (saves setup time).
- Store vectors in **FAISS**, a lightweight local vector database:
  - Use `faiss.IndexFlatL2` for simple similarity search (it compares vectors with Euclidean distance).
  - Save the index to a local file (e.g., `./faiss_index`) for persistence.
- Store each chunk’s text alongside its vector (e.g., in a Python list or dictionary) to show later.

> **What’s a vector?** It’s like a coordinate for a sentence—helps the app find ideas that are “close” to your question.

---

### 4. **Vector Search and Response**
- Let users type a question in Streamlit (e.g., “What’s the main idea of this PDF?”).
- In the FastAPI backend:
  - Convert the question to a vector (same embedding model as above).
  - Search **FAISS** for the top 3–5 most similar text chunks (use L2 distance or cosine for simplicity).
  - Return these chunks as the answer (no LLM needed).
- In Streamlit, show:
  - The question (e.g., “Q: What’s this about?”).
  - The top chunks (e.g., “Here’s the closest text: [chunk 1], [chunk 2]”).
- (Optional) Rank chunks by similarity score and show the score (e.g., “90% match”).

> **How’s this work?** It’s like asking a librarian to find book pages closest to your question and handing you those pages to read.

---

### 5. **Streamlit Frontend**
- Build a simple interface with:
  - **Upload section**: Buttons for PDF upload and URL input.
  - **Question box**: Text area for typing questions (`st.text_area`).
  - **Results**: Show retrieved text chunks clearly (use `st.markdown` for formatting).
  - **Feedback**: Display “Processing…” or errors (e.g., “Invalid URL”).
- Keep it clean:
  - Use `st.columns` or tabs (e.g., “Upload” tab, “Ask” tab).
  - Add clear labels (e.g., “Drop your PDF here”).
- Show at least one question’s results at a time, with a button to ask another.
- (Optional) Add a history of questions and results in a sidebar.

> **Why Streamlit?** It’s an easy way to make a web page where users can upload files and ask questions without learning web design.

---

### 6. **FastAPI Backend**
- Create endpoints to handle:
  - `/upload`: Process PDFs or URLs, chunk text, and store vectors in FAISS.
  - `/query`: Take a question, search FAISS, and return the top text chunks.
- Use JSON to communicate with Streamlit (e.g., send `{ "chunks": ["text1", "text2"] }`).
- Handle errors (e.g., return `{ "error": "Bad PDF" }` if parsing fails).
- Process one file or question at a time to avoid memory crashes.


---

### 7. **Docker Compose Setup (deployment to cloud not required)**
- Use **Docker Compose** to run two services:
  - **Streamlit**: Frontend (port 8501).
  - **FastAPI**: Backend with FAISS (port 8000).
- Write a `docker-compose.yml` to:
  - Set up both services with official images (e.g., `python:3.9-slim` for both).
  - Mount local folders for persistence (e.g., `./uploads`, `./faiss_index`).
  - Link services (FastAPI handles FAISS internally).
- Create a `Dockerfile` for each:
  - Install dependencies (e.g., `streamlit`, `fastapi`, `sentence-transformers`, `faiss-cpu`, `PyPDF2`).
  - Keep it light—use `python:3.9-slim` as the base.
- Test locally with `docker-compose up`.

**Docker Compose Resources**:
- [Official Docker Compose Docs](https://docs.docker.com/compose/) – Basics of `docker-compose.yml`.
- [Docker Compose for Python](https://docs.docker.com/compose/gettingstarted/) – Simple Python example.
- [FAISS Installation Guide](https://github.com/facebookresearch/faiss/wiki/Getting-started) – How to set up FAISS.

> **Why Docker?** It’s like a toolbox—keeps Streamlit and FastAPI ready to run without setup headaches.

---

### 8. **Haystack Option (Optional)**
- Instead of building manually, you can use **Haystack** to simplify:
  - Use Haystack’s `FAISSDocumentStore` for vector storage.
  - Use its `Pipeline` for chunking, embedding, and querying.
  - Check the [Haystack Quickstart](https://docs.haystack.deepset.ai/docs/get-started).
- If you skip Haystack, explain why in your video (e.g., “I wanted to understand FAISS better”).
- Haystack saves time but hides details—both approaches are fine!

> **Why Haystack?** It’s like a RAG shortcut—faster setup but less control over the nuts and bolts.

---

### 9. **Visualization**
- Show in Streamlit:
  - Upload status (e.g., “PDF loaded: 5 pages”).
  - Question and retrieved chunks (e.g., “Q: What’s this? A: [chunk 1], [chunk 2]”).
  - (Optional) Similarity scores (e.g., “Chunk 1: 85% match”).
- Keep it clear:
  - Use colors (e.g., green for success, red for errors).
  - Space elements (e.g., `st.markdown("---")` for dividers).
- (Optional) Add a loading spinner during processing (`st.spinner`).

---

## **Demonstration Video Requirements**

Record a **5–7 minute** video showing your RAG system. Focus on:
- **App walkthrough**:
  - Upload a PDF or URL in Streamlit.
  - Ask 2–3 questions (e.g., “What’s the main topic?” or “What’s mentioned about AI?”).
  - Show the retrieved text chunks.
- **System and algorithms**:
  - Explain in simple terms:
    - “I turn text into vectors with sentence-transformers.”
    - “FAISS finds chunks closest to my question’s vector.”
    - “The app returns those chunks as the answer.”
  - Describe choices (e.g., “I used Haystack for speed” or “I built with FAISS to learn vectors”).
  - Pros and cons (e.g., “FAISS is fast but needs tuning for long texts”).
  - If answers miss the mark, say why (e.g., “My chunks were too short, so it skipped context”).
- **System overview**:
  - How parts connect (e.g., “Streamlit sends questions to FastAPI, which searches FAISS”).
  - Role of Docker (e.g., “It runs everything locally in containers”).
- **Insights**:
  - What you learned (e.g., “Vectors make searching so neat!”).
  - Limits (e.g., “URLs with messy HTML confused my scraper”).
  - How you’d improve (e.g., “I’d try bigger chunks next time”).
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
- Tricky: “What’s not in the text?” (to check if it sticks to the document).
Check:
- PDF/URL processes correctly.
- Retrieved chunks match the question (even if not perfect).
- Docker services (Streamlit, FastAPI) run without crashing.

---

## **Submission Requirements**
- Source code (Streamlit frontend, FastAPI backend).
- `docker-compose.yml` and `Dockerfile`(s).
- Sample PDF or URL used for testing (or a link to it).
- Demonstration video (5–10 minutes).
- Reminder: You do **not** need to deploy this rag on the cloud. But get it working locally!
---

## **Support**
- **Stuck?** DM me on Teams—I reply fast!
- **Confused?** Ask about FAISS, Haystack, or text embeddings in Teams.

