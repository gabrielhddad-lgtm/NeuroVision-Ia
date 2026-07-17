# NeuroVision AI - Development Setup Guide

## Prerequisites

Before you begin, ensure you have the following installed:

### System Requirements
- **OS**: Linux, macOS, or Windows 10+
- **CPU**: 4 cores minimum
- **RAM**: 8 GB minimum
- **Disk**: 50 GB free space for Docker images and databases

### Required Software

#### Backend
- **Python**: 3.13+ ([Download](https://www.python.org/downloads/))
- **pip**: Comes with Python
- **virtualenv**: `pip install virtualenv`

#### Frontend
- **Node.js**: 20+ ([Download](https://nodejs.org/))
- **npm**: Comes with Node.js
- **Yarn** (optional): `npm install -g yarn`

#### Database & Services
- **Docker**: ([Download](https://www.docker.com/products/docker-desktop))
- **Docker Compose**: Comes with Docker Desktop
- **PostgreSQL** (via Docker) or native PostgreSQL 17+

#### Git
- **Git**: ([Download](https://git-scm.com/))

## Step 1: Clone the Repository

```bash
# Clone from GitHub
git clone https://github.com/gabrielhddad-lgtm/NeuroVision-Ia.git
cd NeuroVision-Ia

# Checkout development branch
git checkout phase-1-foundation
```

## Step 2: Start Services with Docker Compose

Docker Compose will start PostgreSQL, Redis, and other required services.

```bash
# Start all services in background
docker-compose up -d

# Verify services are running
docker-compose ps

# View logs
docker-compose logs -f

# Stop services
docker-compose down
```

### Services Started:
- **PostgreSQL**: `localhost:5432`
- **Redis**: `localhost:6379`
- **pgAdmin**: `http://localhost:5050` (optional)

## Step 3: Backend Setup

### Create Virtual Environment

```bash
cd backend

# Create virtual environment
python3.13 -m venv venv

# Activate virtual environment
# On Linux/macOS:
source venv/bin/activate

# On Windows:
venv\Scripts\activate
```

### Install Dependencies

```bash
# Upgrade pip
pip install --upgrade pip

# Install production dependencies
pip install -r requirements.txt

# Install development dependencies
pip install -r requirements-dev.txt
```

### Environment Configuration

Create `.env` file in the `backend/` directory:

```env
# Application
APP_ENV=development
DEBUG=True
SECRET_KEY=your-secret-key-here-change-in-production

# Database
DATABASE_URL=postgresql://neurovision:neurovision_password@localhost:5432/neurovision_db

# Redis
REDIS_URL=redis://localhost:6379/0

# JWT
JWT_SECRET_KEY=your-jwt-secret-key-change-in-production
JWT_ALGORITHM=HS256
JWT_EXPIRATION_HOURS=24
JWT_REFRESH_EXPIRATION_DAYS=30

# CORS
CORS_ORIGINS=["http://localhost:3000", "http://localhost:5173", "http://127.0.0.1:3000"]

# Logging
LOG_LEVEL=INFO
```

### Run Database Migrations

```bash
# Initialize Alembic (if not already done)
alembic init -t generic migrations

# Create initial migration
alembic revision --autogenerate -m "Initial schema"

# Apply migrations
alembic upgrade head

# Verify migrations
alembic current
```

### Start Backend Server

```bash
# Development server with auto-reload
uvicorn app.main:app --reload --host 0.0.0.0 --port 8000

# Or with hot reload and debug
python -m uvicorn app.main:app --reload --port 8000 --log-level debug
```

**Backend available at**: `http://localhost:8000`

## Step 4: Frontend Setup

### Install Dependencies

```bash
cd ../frontend
npm install

# Or with yarn
yarn install
```

### Environment Configuration

Create `.env` file in the `frontend/` directory:

```env
VITE_API_URL=http://localhost:8000
VITE_APP_ENV=development
VITE_LOG_LEVEL=debug
```

### Start Frontend Development Server

```bash
# Start with npm
npm start

# Or with yarn
yarn start

# Or for Electron (after setting up)
npm run electron
```

**Frontend available at**: `http://localhost:5173` (Vite) or `http://localhost:3000` (fallback)

## Step 5: Verify Installation

### Backend Health Check

```bash
# Check API health
curl http://localhost:8000/health

# View API documentation
open http://localhost:8000/docs
```

### Database Connection

```bash
# From backend directory
cd backend

# Connect with Python
python
>>> from app.infrastructure.database import SessionLocal
>>> db = SessionLocal()
>>> print("Connected!")
>>> db.close()
```

### Frontend Check

Open browser at `http://localhost:5173` and verify it loads.

## Step 6: Configure IDE/Editor

### VS Code

Install extensions:
- **Python**: ms-python.python
- **Pylance**: ms-python.vscode-pylance
- **ESLint**: dbaeumer.vscode-eslint
- **Prettier**: esbenp.prettier-vscode
- **Thunder Client**: rangav.vscode-thunder-client
- **PostgreSQL**: ckolkman.vscode-postgres

### PyCharm

1. Open project folder
2. Set Python interpreter to `backend/venv/bin/python`
3. Configure run configurations for FastAPI

## Step 7: Pre-commit Hooks (Optional but Recommended)

```bash
# Install pre-commit
pip install pre-commit

# Install git hooks
pre-commit install

# Run manually
pre-commit run --all-files
```

## Running Tests

### Backend Tests

```bash
cd backend

# Run all tests
pytest

# Run with coverage
pytest --cov=app tests/

# Run specific test file
pytest tests/test_auth.py

# Run with verbose output
pytest -v
```

### Frontend Tests

```bash
cd frontend

# Run all tests
npm test

# Run with coverage
npm test -- --coverage

# Run in watch mode
npm test -- --watch
```

## Common Issues & Troubleshooting

### Port Already in Use

```bash
# Check what's using port 8000
lsof -i :8000

# Kill process
kill -9 <PID>

# Or run on different port
uvicorn app.main:app --port 8001
```

### Database Connection Error

```bash
# Verify Docker services running
docker-compose ps

# Check PostgreSQL logs
docker-compose logs postgres

# Restart services
docker-compose restart
```

### Python Module Import Errors

```bash
# Verify virtual environment is activated
which python  # Should show path to venv

# Reinstall dependencies
pip install --force-reinstall -r requirements.txt
```

### Node Modules Issues

```bash
# Clear npm cache
npm cache clean --force

# Delete node_modules and reinstall
rm -rf node_modules package-lock.json
npm install
```

## Next Steps

1. Read [Architecture Overview](./ARCHITECTURE.md)
2. Review [Development Guide](./DEVELOPMENT.md)
3. Check [Contributing Guidelines](../CONTRIBUTING.md)
4. Start working on issues!

## Support

If you encounter any issues:

1. Check [Troubleshooting](#common-issues--troubleshooting)
2. Search [GitHub Issues](https://github.com/gabrielhddad-lgtm/NeuroVision-Ia/issues)
3. Open a new issue with:
   - Error message
   - Steps to reproduce
   - Environment details
   - Screenshots if applicable

---

**Last Updated**: 2024
