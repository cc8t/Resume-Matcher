# 🐳 Docker Setup Guide for Resume-Matcher

This guide provides instructions to run Resume-Matcher using Docker Compose, eliminating the "nightmare installation" experience by containerizing all components.

## 🚀 Quick Start

### Prerequisites

- **Docker** (version 20.10 or later)
- **Docker Compose** (version 2.0 or later)
- **Git** (to clone the repository)

### Installation

1. **Clone the repository**
   ```bash
   git clone https://github.com/rexeo-asia/Resume-Matcher.git
   cd Resume-Matcher
   ```

2. **Install and setup Ollama** (see [Ollama Setup section](#🤖-ollama-setup-required) for detailed instructions)
   ```bash
   # Install Ollama
   curl -fsSL https://ollama.ai/install.sh | sh
   
   # Pull required models
   ollama pull gemma3:4b
   ollama pull dengcao/Qwen3-Embedding-0.6B:Q8_0
   
   # Start Ollama service (keep running)
   ollama serve
   ```

3. **Configure backend environment**
   Update `apps/backend/.env` with Ollama configuration (see Ollama Setup section)

4. **Start the Docker containers**
   ```bash
   docker compose up -d
   ```

5. **Access the application**
   - Frontend: http://localhost:3000
   - Backend API: http://localhost:8000
   - Ollama API: http://localhost:11434 (running on host)

## 📋 What Gets Containerized

The Docker setup includes:

- **Frontend**: Next.js application (port 3000)
- **Backend**: FastAPI application (port 8000)
- **Database**: SQLite (persisted in Docker volume)

> **Note**: Ollama is not containerized to ensure optimal GPU performance. You'll need to install and run Ollama separately on your host system.

## 🤖 Ollama Setup (Required)

Since Ollama runs outside the container for optimal GPU performance, you need to install and configure it separately:

### 1. Install Ollama

**macOS/Linux:**
```bash
curl -fsSL https://ollama.ai/install.sh | sh
```

**Windows:**
Download from https://ollama.ai/download

### 2. Pull Required Models

Before starting the Docker containers, pull the required AI models:

```bash
# Pull the main language model (~2.6GB)
ollama pull gemma3:4b

# Pull the embedding model (~450MB)
ollama pull dengcao/Qwen3-Embedding-0.6B:Q8_0
```

### 3. Start Ollama Service

```bash
# Start Ollama (runs on port 11434 by default)
ollama serve
```

### 4. Verify Installation

```bash
# Check available models
ollama list

# Test API endpoint
curl http://localhost:11434/api/version
```

### 5. Configure Backend

Update your `apps/backend/.env` file with Ollama configuration:

```env
# LLM Configuration
LLM_PROVIDER=ollama
LLM_MODEL=gemma3:4b
LLM_BASE_URL=http://localhost:11434

# Embedding Configuration
EMBEDDING_PROVIDER=ollama
EMBEDDING_MODEL=dengcao/Qwen3-Embedding-0.6B:Q8_0
EMBEDDING_BASE_URL=http://localhost:11434
```

> **GPU Usage**: Running Ollama on your host system allows it to utilize your GPU for faster inference, which is not possible when running inside a container.

## 🔧 Detailed Setup

### 1. Environment Configuration

The application uses pre-configured environment files:
- `apps/backend/.env` - Backend configuration
- `apps/frontend/.env` - Frontend configuration
- `.env.docker` - Docker-specific variables

### 2. Starting Services

```bash
# Start all services
docker compose up -d

# View logs
docker compose logs -f

# View specific service logs
docker compose logs -f backend
docker compose logs -f frontend
```

### 3. Health Checks

Services include health checks:
- **Backend**: Checks API endpoint health
- **Frontend**: Checks web server availability

> **Note**: Ollama health monitoring is handled separately since it runs on the host system.

## 📁 Volume Persistence

Data is persisted using Docker volumes:
- `resume-matcher-backend-data`: SQLite database and application data

> **Note**: Ollama models and configuration are stored on your host system (typically in `~/.ollama/`), not in Docker volumes.

## 🔧 Management Commands

### Starting and Stopping

```bash
# Start services
docker compose up -d

# Stop services
docker compose down

# Stop and remove volumes (⚠️ deletes data)
docker compose down -v
```

### Rebuilding

```bash
# Rebuild and restart services
docker compose up -d --build

# Rebuild specific service
docker compose build backend
docker compose up -d backend
```

### Viewing Status

```bash
# Check service status
docker compose ps

# View resource usage
docker compose top

# Check volumes
docker volume ls | grep resume-matcher
```

## 🐞 Troubleshooting

### Common Issues

1. **Port Conflicts**
   ```bash
   # Check if ports are in use
   netstat -tlnp | grep -E '(3000|8000|11434)'
   
   # Stop conflicting services or modify ports in compose.yaml
   ```

2. **Ollama Connection Issues**
   ```bash
   # Check if Ollama is running
   curl http://localhost:11434/api/version
   
   # Start Ollama if not running
   ollama serve
   
   # Check available models
   ollama list
   
   # Pull missing models
   ollama pull gemma3:4b
   ollama pull dengcao/Qwen3-Embedding-0.6B:Q8_0
   ```

3. **Build Failures**
   ```bash
   # Clean build cache
   docker compose build --no-cache
   
   # Remove unused images
   docker system prune -a
   ```

4. **Permission Issues**
   ```bash
   # Fix file permissions (Linux/macOS)
   sudo chown -R $USER:$USER .
   ```

### Service-Specific Issues

**Backend Issues:**
```bash
# Check backend logs
docker compose logs -f backend

# Access backend container
docker compose exec backend bash

# Check database
docker compose exec backend ls -la /app/data/
```

**Frontend Issues:**
```bash
# Check frontend logs
docker compose logs -f frontend

# Rebuild frontend
docker compose build frontend --no-cache
docker compose up -d frontend
```

**Ollama Issues (External Service):**
```bash
# Check if Ollama is running
ps aux | grep ollama

# Check available models
ollama list

# Test Ollama API
curl http://localhost:11434/api/version

# Restart Ollama service
killall ollama && ollama serve
```

## 🔐 Security Notes

1. **Change default secrets** in production:
   ```bash
   # Edit apps/backend/.env
   SESSION_SECRET_KEY="your-secure-random-key-here"
   ```

2. **Environment considerations**:
   - Default configuration is for development
   - Use environment-specific `.env` files for production
   - Consider using Docker secrets for sensitive data

## 📊 Resource Requirements

**Minimum Requirements:**
- RAM: 8GB (due to AI models)
- Storage: 10GB free space
- CPU: 4 cores recommended

**Model Sizes:**
- `gemma3:4b`: ~2.6GB
- `dengcao/Qwen3-Embedding-0.6B:Q8_0`: ~450MB

## 🔄 Updates and Maintenance

### Updating the Application

```bash
# Pull latest changes
git pull origin main

# Rebuild and restart
docker compose up -d --build
```

### Updating Models

```bash
# Pull latest model versions (on host system)
ollama pull gemma3:4b
ollama pull dengcao/Qwen3-Embedding-0.6B:Q8_0

# Restart Ollama to ensure updates are loaded
killall ollama && ollama serve
```

### Backup Data

```bash
# Backup Docker volumes
docker run --rm -v resume-matcher-backend-data:/data -v $(pwd):/backup alpine tar czf /backup/backend-backup.tar.gz -C /data .

# Backup Ollama models (from host system)
tar czf ollama-backup.tar.gz -C ~/.ollama .
```

## 🆚 Docker vs Traditional Setup

| Aspect | Traditional Setup | Docker Setup |
|--------|-------------------|--------------|
| Installation Time | 30+ minutes | 5-10 minutes |
| Dependencies | Manual installation | Automatic |
| Environment Issues | Common | Rare |
| Updates | Complex | Simple |
| Cleanup | Manual | Automatic |
| Portability | Low | High |

## 📝 Development

For development with hot-reload:

```yaml
# Add to compose.yaml for development
services:
  frontend:
    volumes:
      - ./apps/frontend:/app
      - /app/node_modules
    command: npm run dev
  
  backend:
    volumes:
      - ./apps/backend:/app
    command: uvicorn app.main:app --reload --host 0.0.0.0 --port 8000
```

---

**Need help?** Open an issue on the GitHub repository with your Docker setup questions.

*This Docker setup eliminates the complex installation process described in the original SETUP.md, providing a one-command solution for running Resume-Matcher locally.*
