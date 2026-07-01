# HLD — RecuerdoAI (High Level Design)

## 1. System Overview

RecuerdoAI is a local-first AI system that enables semantic search over voice messages and spoken conversations. It converts audio into text, indexes it using embeddings, and allows users to query their memory using natural language.

The system is designed to run primarily on a local machine (Linux during development) with a future goal of full on-device inference on mobile.

---

## 2. High-Level Architecture

User (Android App / CLI)
↓
Query Interface
↓
FastAPI Backend (Python)
↓
AI Processing Layer
    - Speech-to-Text (Whisper / Faster-Whisper)
    - Text Chunking
    - Embedding Model (BGE / sentence-transformers)
    - Vector Search (FAISS)
    - LLM (optional reasoning via Ollama)
↓
Storage Layer
    - Audio Files (local filesystem)
    - Transcripts (text)
    - Metadata (SQLite)
    - Embeddings (FAISS index)

---

## 3. Core Components

### 3.1 Client Layer (Android App)
- Accepts user queries in natural language
- Displays AI-generated answers
- Shows matching voice messages
- Allows playback at correct timestamps

Tech:
- Kotlin
- Jetpack Compose
- ExoPlayer

---

### 3.2 Backend API Layer
- Handles ingestion and search requests
- Orchestrates AI pipeline
- Communicates with Android client

Tech:
- FastAPI
- Uvicorn

---

### 3.3 Speech-to-Text Module
- Converts voice messages into text
- Optional timestamp alignment

Tech:
- Whisper
- Faster-Whisper (recommended)
- whisper.cpp (for future on-device deployment)

---

### 3.4 Embedding Module
- Converts transcript chunks into vector representations
- Enables semantic search

Tech:
- BGE-small / BGE-m3
- sentence-transformers

---

### 3.5 Vector Database
- Stores embeddings
- Performs similarity search

Tech:
- FAISS (local, fast, lightweight)

---

### 3.6 LLM Module (Optional)
- Generates natural language answers from retrieved chunks
- Summarizes multiple voice messages

Tech:
- Ollama
- Qwen2.5 / Llama models

---

### 3.7 Storage Layer
- Stores raw audio files
- Stores transcripts
- Stores metadata (chat ID, timestamp, speaker)

Tech:
- SQLite (metadata)
- Local filesystem (audio)

---

## 4. Data Flow

### 4.1 Ingestion Pipeline

Audio Message
→ Store locally
→ Speech-to-Text (Whisper)
→ Generate transcript
→ Split into chunks
→ Generate embeddings
→ Store in FAISS + SQLite

---

### 4.2 Query Pipeline

User Query
→ Convert to embedding
→ FAISS similarity search
→ Retrieve top-k relevant chunks
→ (Optional) LLM generates response
→ Return:
   - Answer
   - Matching transcript
   - Timestamp
   - Audio reference for playback

---

## 5. Deployment Architecture

### Phase 1 (Development)
- All AI models run on Linux machine
- FastAPI server runs locally
- Android app connects via local network or tunneling (ngrok / cloudflared)

---

### Phase 2 (MVP)
- Lightweight backend service packaged for distribution
- Optimized model inference (quantized models if needed)

---

### Phase 3 (Future)
- Fully on-device inference (Android)
- No backend required
- Whisper + embeddings running locally on mobile

---

## 6. Performance Considerations
- Speech-to-text is the most expensive operation
- Embeddings are computed once and cached
- FAISS provides fast similarity search (sub-second retrieval)
- LLM usage is optional to reduce latency

---

## 7. Security & Privacy
- Data is stored locally by default
- No cloud dependency required for core functionality
- Future goal: fully offline processing
- Optional encryption for stored audio and embeddings

---

## 8. Scalability
The system is designed to scale from a single-user local system to large datasets:

- FAISS supports large-scale vector search
- Modular pipeline allows replacement of components
- Future upgrades:
  - distributed vector DB
  - GPU inference servers
  - mobile edge inference

---

## 9. Key Design Principles
- Local-first architecture
- Privacy by design
- Modular AI components
- Fast semantic retrieval
- Offline capability as long-term goal

---

## 10. Summary

RecuerdoAI transforms voice messages into a searchable memory system using speech recognition, embeddings, and vector search. It enables users to retrieve spoken information using natural language queries instead of manually replaying audio.
