# RecuerdoAI

**Search your voice messages like text. Turn spoken conversations into searchable memory using AI.**

---

## 🚀 Overview

RecuerdoAI is a local-first AI system that converts voice messages into searchable memory.

It enables users to:
- Transcribe voice messages
- Index conversations using embeddings
- Search spoken content using natural language
- Retrieve exact audio moments with timestamps

Think of it as:
> “Google Search for your voice conversations”

---

## 🎯 Problem

Voice messages are widely used in apps like WhatsApp and Telegram, but:

- They cannot be searched
- Important information gets lost in long audio histories
- Users must manually replay messages to find information
- There is no semantic understanding of spoken content

---

## 💡 Solution

RecuerdoAI converts voice into structured memory:

1. Audio → Text (Speech-to-Text)
2. Text → Embeddings (semantic understanding)
3. Embeddings → Vector Search (fast retrieval)
4. Query → Natural language search over voice history

---

## 🧠 Key Features

- 🎙 Voice message transcription (Whisper / Faster-Whisper)
- 🔎 Semantic search over conversations
- ⚡ Fast vector search using FAISS
- 📍 Timestamp-based audio retrieval
- 🧩 Modular AI pipeline (replaceable components)
- 🔒 Local-first architecture (privacy-focused)

---

## 🏗 Architecture

Audio Message
→ Speech-to-Text (Whisper)
→ Chunking + Processing
→ Embeddings (BGE / sentence-transformers)
→ Vector Store (FAISS)
→ Search Engine (Query → Semantic Match)
→ Results + Audio Playback

---

## 📦 Tech Stack

### Backend
- Python
- FastAPI
- Whisper / Faster-Whisper
- FAISS
- sentence-transformers

### Storage
- SQLite (metadata)
- Local filesystem (audio)

### Future (planned)
- Android app (Kotlin)
- On-device inference (whisper.cpp)
- Fully offline mode

---

## 🔌 API Overview

### POST /ingest

Upload a voice message for processing.

Request:
{
  "file": "audio.mp3",
  "chat_id": "123"
}

---

### POST /search

Search voice memory using natural language.

Request:
{
  "query": "What did I say about project deadline?"
}

Response:
{
  "answer": "You mentioned the deadline is Friday.",
  "results": [
    {
      "text": "The deadline is Friday",
      "timestamp": 12.5,
      "audio_id": "abc123",
      "score": 0.87
    }
  ]
}

---

## ⚙️ How It Works

1. Ingestion
- Audio uploaded
- Stored locally
- Processing triggered

2. Processing
- Speech-to-text conversion
- Text chunking
- Embedding generation
- Stored in FAISS + SQLite

3. Search
- User query embedded
- FAISS retrieves similar chunks
- Results returned with timestamps

---

## 🔐 Privacy

- Local-first architecture
- No cloud required for core system
- Audio remains on user device (future goal)
- Optional encryption planned

---

## 📌 Status

MVP Phase:
- Architecture complete
- Backend implementation in progress
- Core pipeline defined

---

## 🛣 Roadmap

- WhatsApp / Telegram integration
- Android app (Jetpack Compose)
- Real-time voice indexing
- On-device inference
- Fully offline mode
- Memory summarization layer

---

## 🧪 Example Use Case

User:
"What did my friend say about meeting time?"

RecuerdoAI:
- Finds relevant voice message
- Extracts spoken content
- Returns timestamp + playback

---

## 📖 Philosophy

If you can say it, you should be able to search it.

---

## 👤 Author

Built by Dhruv  
Project: RecuerdoAI
