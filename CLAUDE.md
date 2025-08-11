# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Development Commands

### Running the Application
```bash
# Quick start (recommended)
./run.sh

# Manual start
cd backend && uv run uvicorn app:app --reload --port 8000
```

### Environment Setup
```bash
# Install dependencies
uv sync

# Create .env file with required API key
echo "ANTHROPIC_API_KEY=your_key_here" > .env
```

### Development Server
- Web Interface: http://localhost:8000
- API Documentation: http://localhost:8000/docs

## Architecture Overview

### RAG System Architecture
This is a Retrieval-Augmented Generation (RAG) system with a clear separation between document processing, vector storage, AI generation, and web interface.

**Core Flow**: Documents → Processing → Chunking → Vector Storage → Semantic Search → AI Response

### Key Components

**RAGSystem** (`backend/rag_system.py`)
- Main orchestrator that coordinates all components
- Handles document ingestion, query processing, and response generation
- Manages tool-based search architecture

**Document Processing Pipeline** (`backend/document_processor.py`)
- Parses structured course documents with specific format expectations
- Intelligent sentence-based chunking with configurable overlap
- Extracts course metadata (title, instructor, lesson structure)
- Adds contextual prefixes to chunks for better semantic search

**Vector Storage** (`backend/vector_store.py`)
- ChromaDB-based persistent storage with SentenceTransformers embeddings
- Separate collections for course metadata and content chunks
- Deduplication logic to prevent reprocessing existing courses

**AI Generation** (`backend/ai_generator.py`)
- Anthropic Claude integration with tool support
- Specialized system prompt for educational content
- Tool-based search approach (not direct vector similarity)

**Search Tools** (`backend/search_tools.py`)
- Course search tool that interfaces with vector store
- Tool manager for handling multiple search capabilities
- Source tracking for response attribution

### Document Format Expectations
Course documents must follow this structure:
```
Course Title: [title]
Course Link: [url]
Course Instructor: [instructor]

Lesson 0: [lesson title]
Lesson Link: [optional lesson link]
[lesson content...]

Lesson 1: [next lesson]
[content continues...]
```

### Configuration System
All settings centralized in `backend/config.py`:
- Chunk size (800 chars) and overlap (100 chars)
- Embedding model (all-MiniLM-L6-v2)
- ChromaDB path and search limits
- Claude model and API configuration

### Session Management
- Conversation history tracking with configurable limits
- Session-based context preservation for multi-turn conversations

### Frontend Integration
- FastAPI serves both API endpoints and static frontend files
- CORS configured for development with hot reloading support
- Real-time course statistics and query interface

## Key Development Patterns

### Adding New Documents
- Place .txt files in `docs/` folder
- Application auto-loads on startup
- Existing course detection prevents duplicates

### Modifying Search Behavior
- Adjust `CHUNK_SIZE` and `CHUNK_OVERLAP` in config.py
- Modify context prefixes in document_processor.py
- Tool-based search logic in search_tools.py

### AI Response Customization
- System prompt in ai_generator.py controls response style
- Tool definitions in search_tools.py determine search capabilities
- Temperature and token limits configurable in config.py