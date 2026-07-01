# LLD — RecuerdoAI (Low Level Design)

## 1. Overview

RecuerdoAI is a local-first AI system that converts voice messages into searchable memory using speech-to-text, embeddings, vector search, and optional LLM reasoning.

The system is modular so each AI component can be replaced independently (Whisper, embedding models, vector DB, LLM).

---

## 2. System Modules

The system consists of six core modules:

1. Ingestion Module
2. Speech-to-Text Module
3. Chunking & Processing Module
4. Embedding Module
5. Vector Search Module (FAISS)
6. Query & Response Module
7. Optional LLM Module

---

## 3. Data Models

### AudioMessage

class AudioMessage:
    id: str
    file_path: str
    duration: float
    created_at: datetime
    chat_id: str

---

### TranscriptChunk

class TranscriptChunk:
    id: str
    audio_id: str
    text: str
    start_time: float
    end_time: float

---

### EmbeddingVector

class EmbeddingVector:
    chunk_id: str
    vector: list[float]

---

### SearchResult

class SearchResult:
    chunk_id: str
    score: float
    text: str
    audio_path: str
    timestamp: float

---

## 4. Module Design

---

## 4.1 Ingestion Module

Responsibility:
- Accept audio files
- Store locally
- Trigger processing pipeline

class IngestionService:

    def save_audio(self, file: bytes, metadata: dict) -> AudioMessage:
        pass

    def trigger_pipeline(self, audio_id: str):
        pass

---

## 4.2 Speech-to-Text Module

Responsibility:
- Convert audio into text with timestamps

class SpeechToTextService:

    def transcribe(self, file_path: str) -> list:
        """
        Returns:
        [
            {"text": "...", "start": 0.0, "end": 3.2}
        ]
        """
        pass

Recommended models:
- Whisper
- Faster-Whisper
- whisper.cpp (future on-device)

---

## 4.3 Chunking Module

Responsibility:
- Split transcripts into semantic chunks
- Preserve timestamp mapping

class ChunkingService:

    def chunk(self, transcript: list) -> list[TranscriptChunk]:
        pass

Strategy:
- Sentence-based splitting
- Max token limits per chunk
- Preserve time alignment

---

## 4.4 Embedding Module

Responsibility:
- Convert text into vector embeddings

class EmbeddingService:

    def __init__(self, model):
        self.model = model

    def embed(self, text: str) -> list[float]:
        pass

Recommended models:
- BGE-small-en-v1.5
- BGE-m3 (multilingual)

---

## 4.5 Vector Database (FAISS)

Responsibility:
- Store embeddings
- Perform similarity search

class VectorStore:

    def __init__(self):
        self.index = faiss.IndexFlatL2(dim)

    def add(self, vector: list[float], metadata: dict):
        pass

    def search(self, query_vector: list[float], top_k: int):
        pass

Metadata is stored separately in SQLite:
- chunk_id
- audio_id
- timestamp
- text

---

## 4.6 Query Service

Responsibility:
- Handle user queries
- Run embedding + search pipeline
- Return ranked results

class QueryService:

    def __init__(self, embedder, vector_store, llm=None):
        self.embedder = embedder
        self.vector_store = vector_store
        self.llm = llm

    def search(self, query: str):
        query_vector = self.embedder.embed(query)
        results = self.vector_store.search(query_vector, top_k=5)
        return results

    def generate_answer(self, query: str, results):
        if self.llm:
            return self.llm.generate(query, results)
        return results

---

## 4.7 LLM Module (Optional)

Responsibility:
- Generate natural language answers
- Summarize multiple voice messages

class LLMService:

    def generate(self, query: str, context: list) -> str:
        prompt = self.build_prompt(query, context)
        return self.model.generate(prompt)

Recommended runtimes:
- Ollama
- llama.cpp
- Qwen2.5 3B Instruct

---

## 5. API Layer (FastAPI)

Endpoints:

---

POST /ingest

@app.post("/ingest")
def ingest_audio(file: UploadFile):
    pass

---

POST /search

@app.post("/search")
def search(query: str):
    pass

Response:

{
    "answer": "...",
    "results": [
        {
            "text": "...",
            "timestamp": 12.5,
            "audio_id": "123",
            "score": 0.87
        }
    ]
}

---

GET /health

@app.get("/health")
def health():
    return {"status": "ok"}

---

## 6. Processing Pipeline

---

Ingestion Flow:

Audio File
→ Save locally
→ Speech-to-Text (Whisper)
→ Generate transcript
→ Chunk text
→ Create embeddings
→ Store in FAISS + SQLite

---

Query Flow:

User Query
→ Convert to embedding
→ FAISS similarity search
→ Retrieve top-k chunks
→ (Optional) LLM reasoning
→ Return answer + timestamps + audio references

---

## 7. Storage Design

SQLite Tables:

audio_messages:
- id
- file_path
- chat_id
- timestamp

transcript_chunks:
- id
- audio_id
- text
- start_time
- end_time

embeddings:
- chunk_id
- vector reference

Audio files stored in local filesystem.

---

## 8. Performance Considerations

- Whisper is the most expensive step (run async)
- Embeddings are computed once and cached
- FAISS provides fast similarity search (sub-second)
- LLM is optional for reducing latency overhead

---

## 9. Design Principles

- Local-first architecture
- Privacy by default
- Modular AI components
- Replaceable models (Whisper, embeddings, LLM)
- Offline-capable future design

---

## 10. Summary

RecuerdoAI transforms voice messages into a semantic memory system using speech recognition, embeddings, and vector search. It enables users to query spoken conversations using natural language instead of manually searching or replaying audio files.
