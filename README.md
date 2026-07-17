# NeuroVision AI

**AI-Powered Clinical Decision Support System for EEG Analysis**

## Overview

NeuroVision AI is an enterprise-grade, modular Clinical Decision Support System (CDSS) designed to assist healthcare professionals during Electroencephalogram (EEG) examinations. The platform combines advanced signal processing, machine learning, and secure patient record management to improve workflow efficiency, reduce report preparation time, and enhance clinical decision-making.

**Important**: NeuroVision AI is designed to **support, not replace** physician judgment. Every AI-generated finding requires physician review and approval before becoming part of the official medical record.

## Key Features

- 🧠 **Advanced EEG Analysis**: Signal quality assessment, artifact detection, pattern recognition
- 🔒 **Secure Patient Management**: HIPAA/LGPD/GDPR-oriented architecture with full audit logging
- 📊 **Multi-Hospital Support**: Enterprise-grade infrastructure for managing multiple facilities
- 🤖 **AI-Assisted Reporting**: Configurable LLM integration for preliminary report drafting
- 👨‍⚕️ **Physician-Centric Workflow**: Comprehensive review and approval process
- 📱 **Modern UI**: React + Electron desktop application with real-time EEG visualization
- 🔧 **Modular Architecture**: Clean Architecture, DDD, and Hexagonal design for scalability
- 🐳 **Container-Ready**: Docker and Kubernetes-ready deployment

## Technology Stack

### Backend
- **Framework**: FastAPI (Python 3.13)
- **Database**: PostgreSQL 17
- **ORM**: SQLAlchemy
- **Task Queue**: Celery + Redis
- **Validation**: Pydantic v2
- **Migrations**: Alembic

### Frontend
- **Framework**: React 19
- **Language**: TypeScript
- **Desktop**: Electron
- **Styling**: TailwindCSS
- **State Management**: React Query
- **Routing**: React Router

### AI & Signal Processing
- **Deep Learning**: PyTorch
- **EEG Processing**: MNE-Python
- **Numerical Computing**: NumPy, SciPy
- **Machine Learning**: Scikit-Learn
- **Model Deployment**: ONNX Runtime

### Infrastructure
- **Containerization**: Docker, Docker Compose
- **CI/CD**: GitHub Actions
- **Web Server**: NGINX
- **Testing**: pytest, Jest, Playwright

## Architecture Overview

NeuroVision AI implements:

- **Clean Architecture**: Separation of concerns (Entities → Use Cases → Interface Adapters → Frameworks)
- **Domain-Driven Design (DDD)**: Bounded contexts around clinical domains
- **Hexagonal Architecture**: Flexible, testable, framework-agnostic design
- **Event-Driven Architecture**: Asynchronous processing for long-running tasks
- **CQRS**: Command Query Responsibility Segregation where appropriate
- **Dependency Injection**: Loose coupling and testability
- **Repository Pattern**: Data abstraction layer

The architecture is designed to evolve from a modular monolith into microservices without major refactoring.

## Project Structure

```
neurovision-ai/
├── backend/                    # FastAPI backend
│   ├── app/                    # Application code
│   │   ├── api/               # API routes
│   │   ├── core/              # Core configuration
│   │   ├── domain/            # Domain models (DDD)
│   │   ├── application/        # Use cases and services
│   │   ├── infrastructure/     # Database, external services
│   │   └── shared/            # Shared utilities
│   ├── tests/                  # Test suite
│   ├── migrations/             # Alembic migrations
│   └── requirements.txt        # Python dependencies
├── frontend/                   # React + Electron frontend
│   ├── src/
│   │   ├── components/        # React components
│   │   ├── pages/             # Page components
│   │   ├── hooks/             # Custom React hooks
│   │   ├── services/          # API clients
│   │   ├── store/             # State management
│   │   ├── types/             # TypeScript types
│   │   └── utils/             # Utility functions
│   ├── public/                # Static assets
│   └── package.json           # Dependencies
├── ai_engine/                  # AI processing pipeline
│   ├── eeg_processor/         # EEG signal processing
│   ├── models/                # ML models
│   ├── feature_extraction/    # Feature engineering
│   └── explainability/        # Explainable AI layer
├── infrastructure/             # Infrastructure as Code
│   ├── docker/                # Docker configurations
│   ├── k8s/                   # Kubernetes manifests (future)
│   └── nginx/                 # NGINX configuration
├── docs/                       # Documentation
│   ├── architecture/          # Architecture decisions
│   ├── api/                   # API documentation
│   └── deployment/            # Deployment guides
├── .github/                    # GitHub configurations
│   ├── workflows/             # GitHub Actions
│   ├── ISSUE_TEMPLATE/        # Issue templates
│   └── PULL_REQUEST_TEMPLATE/ # PR template
└── docker-compose.yml         # Local development setup
```

## Getting Started

### Prerequisites
- Python 3.13+
- Node.js 20+
- PostgreSQL 17+
- Docker & Docker Compose
- Git

### Local Development Setup

```bash
# Clone the repository
git clone https://github.com/gabrielhddad-lgtm/NeuroVision-Ia.git
cd NeuroVision-Ia

# Checkout the phase-1-foundation branch
git checkout phase-1-foundation

# Start services with Docker Compose
docker-compose up -d

# Backend setup
cd backend
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
pip install -r requirements.txt

# Run migrations
alembic upgrade head

# Start FastAPI server
uvicorn app.main:app --reload --port 8000

# Frontend setup (in another terminal)
cd frontend
npm install
npm start
```

The application will be available at:
- **Backend API**: http://localhost:8000
- **API Documentation**: http://localhost:8000/docs
- **Frontend**: http://localhost:3000

## Security & Compliance

NeuroVision AI is designed with healthcare privacy and security requirements in mind:

- ✅ **JWT Authentication** with refresh tokens
- ✅ **Role-Based Access Control (RBAC)** with granular permissions
- ✅ **AES-256 Encryption** for sensitive data
- ✅ **Full Audit Logging** for compliance
- ✅ **HTTPS/TLS** support
- ✅ **LGPD, HIPAA, GDPR** oriented architecture
- ✅ **Secure Secrets Management**
- ✅ **OWASP Top 10** compliance

## Development Phases

### Phase 1: Foundation ✅
- Project architecture
- Setup and configuration
- Development environment

### Phase 2: Database
- PostgreSQL schema
- Entities and relationships
- Migrations

### Phase 3: Authentication & Security
- JWT implementation
- RBAC system
- Audit logging

### Phase 4: Patient Management
- EMR module
- Medical records
- Patient search and timeline

### Phase 5: EEG Module
- Signal visualization
- File format support (EDF, EDF+, BDF, CSV)
- Real-time processing

### Phase 6: AI Processing Engine
- Signal quality analysis
- Artifact detection
- Feature extraction
- Pattern recognition

### Phase 7: Clinical Report Generation
- LLM integration
- Preliminary report drafting
- Physician review workflow

### Phase 8: Dashboard
- Modern UI
- Patient list
- EEG viewer
- Report editor

### Phase 9: Testing
- Unit tests
- Integration tests
- E2E tests

### Phase 10: Deployment
- Production deployment
- CI/CD pipelines
- Monitoring and logging

## Documentation

Detailed documentation is available in the `docs/` directory:

- **[Architecture Guide](docs/architecture/README.md)**: System design and architectural decisions
- **[API Documentation](docs/api/README.md)**: REST API reference
- **[Development Guide](docs/DEVELOPMENT.md)**: Development workflow and guidelines
- **[Deployment Guide](docs/deployment/README.md)**: Production deployment instructions

## Contributing

We welcome contributions! Please read [CONTRIBUTING.md](CONTRIBUTING.md) for details on our code of conduct and the process for submitting pull requests.

## Code of Conduct

This project adheres to the Contributor Covenant [Code of Conduct](CODE_OF_CONDUCT.md).

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Author

Gabriel Haddad - [GitHub](https://github.com/gabrielhddad-lgtm)

## Acknowledgments

- Built with ❤️ for healthcare professionals and patients
- Inspired by the need for responsible, explainable AI in clinical settings
- Special thanks to the open-source community

## Contact & Support

For questions, issues, or collaboration opportunities:
- 📧 Open an issue on GitHub
- 🐛 Report bugs through GitHub Issues
- 💡 Suggest features through GitHub Discussions

---

**NeuroVision AI** – Empowering Clinical Decision Support Through Responsible AI
