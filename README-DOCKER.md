# 🐳 Quick Docker Setup

**Skip the complicated setup!** Run Resume-Matcher with Docker in just a few commands:

## Quick Installation

```bash
# 1. Clone the repository
git clone https://github.com/rexeo-asia/Resume-Matcher.git
cd Resume-Matcher

# 2. Install and setup Ollama (for GPU acceleration)
curl -fsSL https://ollama.ai/install.sh | sh
ollama pull gemma3:4b
ollama pull dengcao/Qwen3-Embedding-0.6B:Q8_0
ollama serve &  # Run in background

# 3. Configure backend (update apps/backend/.env with Ollama settings)
# 4. Start containers
docker compose up -d
```

## Access Points

- **Web App**: http://localhost:3000
- **API**: http://localhost:8000  
- **Ollama**: http://localhost:11434 (runs on host for GPU support)

## What You Get

✅ **Frontend**: Next.js web interface (containerized)  
✅ **Backend**: FastAPI server (containerized)  
✅ **AI Models**: Ollama models with GPU acceleration (host-based)  
✅ **Database**: Persistent SQLite storage (containerized)  
✅ **Optimized Performance**: GPU-accelerated AI inference

## Management

```bash
# View logs
docker compose logs -f

# Stop services  
docker compose down

# Update and rebuild
git pull && docker compose up -d --build
```

**Full documentation**: See [DOCKER-SETUP.md](./DOCKER-SETUP.md)

---

*Minimal Python/Node.js dependencies with optimized GPU-accelerated AI inference!*

> **Why external Ollama?** Running Ollama on your host system (instead of in a container) enables GPU acceleration, resulting in significantly faster AI model inference.
