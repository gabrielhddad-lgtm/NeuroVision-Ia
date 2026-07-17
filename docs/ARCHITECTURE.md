# NeuroVision AI - Architecture Overview

## Executive Summary

NeuroVision AI is designed as a **modular monolith** using **Clean Architecture**, **Domain-Driven Design (DDD)**, and **Hexagonal Architecture** principles. This design allows the system to scale horizontally and eventually evolve into microservices without major refactoring.

## Architectural Principles

### 1. Clean Architecture

The application is organized into four layers, from innermost to outermost:

```
┌─────────────────────────────────────────┐
│  Frameworks & Drivers (HTTP, DB, UI)    │ ← External
├─────────────────────────────────────────┤
│  Interface Adapters (Controllers, DTOs) │
├─────────────────────────────────────────┤
│  Application Logic (Use Cases, Services)│
├─────────────────────────────────────────┤
│  Enterprise Business Logic (Entities)   │ ← Internal
└─────────────────────────────────────────┘
```

**Layer Responsibilities**:

- **Entities** (Core): Domain models with business logic
- **Use Cases** (Application): Orchestrate business logic
- **Interface Adapters** (Presentation): Convert between use cases and external formats
- **Frameworks & Drivers** (Infrastructure): Databases, web frameworks, external services

### 2. Domain-Driven Design (DDD)

The domain is organized into **bounded contexts**:

```
NeuroVision AI Domain
├── Patient Context
│   ├── Patient Aggregate
│   ├── Medical History Value Object
│   └── Patient Repository
├── EEG Context
│   ├── EEGExam Aggregate
│   ├── Signal Value Object
│   └── EEGExam Repository
├── Clinical Context
│   ├── Report Aggregate
│   ├── Finding Value Object
│   └── Report Repository
├── Authentication Context
│   ├── User Aggregate
│   ├── Permission Value Object
│   └── User Repository
└── AI Context
    ├── Analysis Aggregate
    ├── Prediction Value Object
    └── Analysis Repository
```

**Key DDD Concepts**:

- **Aggregates**: Clusters of entities that act as a consistency boundary
- **Repositories**: Persist and retrieve aggregates
- **Value Objects**: Immutable objects without identity
- **Domain Events**: Capture domain state changes
- **Bounded Contexts**: Clear boundaries around related concepts

### 3. Hexagonal Architecture (Ports & Adapters)

```
          ┌─────────────────┐
          │   HTTP Client   │
          └────────┬────────┘
                   │
         ┌─────────▼──────────┐
         │  HTTP Adapter      │ ← Port: REST API
         └─────────┬──────────┘
                   │
      ┌────────────▼─────────────┐
      │  Application Core        │
      │  (Use Cases, Services)   │
      └────────────┬─────────────┘
                   │
    ┌──────────────┼──────────────┐
    │              │              │
┌───▼────┐  ┌─────▼────┐  ┌─────▼────┐
│Database │  │  Cache   │  │  Queue   │
│Adapter  │  │ Adapter  │  │ Adapter  │
└─────────┘  └──────────┘  └──────────┘
```

The core application depends only on abstracted interfaces (ports), while implementations (adapters) can be swapped.

### 4. Event-Driven Architecture

Key domain events are published and can trigger side effects:

```
Patient Registered
    ↓
    ├→ Send Welcome Email
    ├→ Create Default Preferences
    └→ Initialize Audit Log

EEG Exam Submitted
    ↓
    ├→ Trigger AI Analysis
    ├→ Notify Physician
    └→ Update Patient Timeline

AI Analysis Complete
    ↓
    ├→ Generate Draft Report
    ├→ Notify Physician
    └→ Update Analysis Status
```

### 5. CQRS (Command Query Responsibility Segregation)

Separate read and write operations for improved performance:

```
Writes (Commands)       Reads (Queries)
    ↓                       ↓
Create Patient      →   Get Patient
Update Report       →   List Reports
Submit Analysis     →   Search EEG Exams
```

## Directory Structure

```
neuro-vision-ai/
├── backend/                          # FastAPI backend
│   ├── app/
│   │   ├── __init__.py
│   │   ├── main.py                  # FastAPI app entry point
│   │   ├── api/                     # API layer (routes/controllers)
│   │   │   ├── __init__.py
│   │   │   ├── v1/                  # API v1
│   │   │   │   ├── __init__.py
│   │   │   │   ├── patients.py      # Patient routes
│   │   │   │   ├── eeg_exams.py     # EEG routes
│   │   │   │   ├── reports.py       # Report routes
│   │   │   │   ├── auth.py          # Auth routes
│   │   │   │   └── admin.py         # Admin routes
│   │   │   └── dependencies.py      # Shared dependencies
│   │   ├── core/                    # Core configuration
│   │   │   ├── __init__.py
│   │   │   ├── config.py            # Configuration management
│   │   │   ├── constants.py         # Application constants
│   │   │   ├── security.py          # Security utilities
│   │   │   └── exceptions.py        # Custom exceptions
│   │   ├── domain/                  # DDD domain layer
│   │   │   ├── __init__.py
│   │   │   ├── entities/            # Domain entities
│   │   │   │   ├── __init__.py
│   │   │   │   ├── user.py
│   │   │   │   ├── patient.py
│   │   │   │   ├── eeg_exam.py
│   │   │   │   ├── report.py
│   │   │   │   └── analysis.py
│   │   │   ├── value_objects/       # Value objects
│   │   │   │   ├── __init__.py
│   │   │   │   ├── permission.py
│   │   │   │   ├── signal.py
│   │   │   │   └── finding.py
│   │   │   ├── repositories/        # Repository interfaces
│   │   │   │   ├── __init__.py
│   │   │   │   ├── user_repository.py
│   │   │   │   ├── patient_repository.py
│   │   │   │   ├── eeg_exam_repository.py
│   │   │   │   └── report_repository.py
│   │   │   ├── events/              # Domain events
│   │   │   │   ├── __init__.py
│   │   │   │   ├── patient_events.py
│   │   │   │   ├── eeg_events.py
│   │   │   │   └── report_events.py
│   │   │   └── services/            # Domain services
│   │   │       ├── __init__.py
│   │   │       └── permission_service.py
│   │   ├── application/             # Use cases and services
│   │   │   ├── __init__.py
│   │   │   ├── use_cases/           # Application use cases
│   │   │   │   ├── __init__.py
│   │   │   │   ├── patient_use_cases.py
│   │   │   │   ├── eeg_use_cases.py
│   │   │   │   ├── report_use_cases.py
│   │   │   │   └── auth_use_cases.py
│   │   │   ├── services/            # Application services
│   │   │   │   ├── __init__.py
│   │   │   │   ├── patient_service.py
│   │   │   │   ├── eeg_service.py
│   │   │   │   ├── report_service.py
│   │   │   │   └── auth_service.py
│   │   │   ├── dtos/                # Data transfer objects
│   │   │   │   ├── __init__.py
│   │   │   │   ├── patient_dto.py
│   │   │   │   ├── eeg_dto.py
│   │   │   │   └── report_dto.py
│   │   │   ├── queries/             # Query handlers (CQRS)
│   │   │   │   ├── __init__.py
│   │   │   │   └── patient_queries.py
│   │   │   ├── commands/            # Command handlers (CQRS)
│   │   │   │   ├── __init__.py
│   │   │   │   └── patient_commands.py
│   │   │   └── event_handlers/      # Event handlers
│   │   │       ├── __init__.py
│   │   │       └── patient_event_handlers.py
│   │   ├── infrastructure/          # Infrastructure layer
│   │   │   ├── __init__.py
│   │   │   ├── database/            # Database layer
│   │   │   │   ├── __init__.py
│   │   │   │   ├── db.py            # Database connection
│   │   │   │   ├── models.py        # SQLAlchemy models
│   │   │   │   └── repositories/    # Repository implementations
│   │   │   │       ├── __init__.py
│   │   │   │       ├── user_repository_impl.py
│   │   │   │       ├── patient_repository_impl.py
│   │   │   │       └── eeg_exam_repository_impl.py
│   │   │   ├── cache/               # Caching layer
│   │   │   │   ├── __init__.py
│   │   │   │   └── redis_cache.py
│   │   │   ├── queue/               # Message queue
│   │   │   │   ├── __init__.py
│   │   │   │   └── celery_config.py
│   │   │   ├── external/            # External services
│   │   │   │   ├── __init__.py
│   │   │   │   ├── email_service.py
│   │   │   │   └── notification_service.py
│   │   │   └── logging/             # Logging configuration
│   │   │       ├── __init__.py
│   │   │       └── logger.py
│   │   └── shared/                  # Shared utilities
│   │       ├── __init__.py
│   │       ├── dependencies.py      # Dependency injection
│   │       ├── middleware/          # Custom middleware
│   │       │   ├── __init__.py
│   │       │   ├── auth_middleware.py
│   │       │   ├── error_middleware.py
│   │       │   └── logging_middleware.py
│   │       ├── schemas/             # Pydantic schemas
│   │       │   ├── __init__.py
│   │       │   ├── responses.py
│   │       │   └── errors.py
│   │       └── utils/               # Utility functions
│   │           ├── __init__.py
│   │           ├── validators.py
│   │           └── formatters.py
│   ├── tests/                       # Test suite
│   │   ├── __init__.py
│   │   ├── conftest.py              # pytest configuration
│   │   ├── unit/                    # Unit tests
│   │   │   ├── __init__.py
│   │   │   ├── domain/
│   │   │   ├── application/
│   │   │   └── shared/
│   │   ├── integration/             # Integration tests
│   │   │   ├── __init__.py
│   │   │   ├── test_api_auth.py
│   │   │   ├── test_api_patients.py
│   │   │   └── test_database.py
│   │   └── e2e/                     # End-to-end tests
│   │       ├── __init__.py
│   │       └── test_clinical_workflow.py
│   ├── migrations/                  # Alembic migrations
│   │   ├── versions/
│   │   ├── env.py
│   │   ├── script.py.mako
│   │   └── alembic.ini
│   ├── requirements.txt             # Python dependencies
│   ├── requirements-dev.txt         # Development dependencies
│   ├── .env.example                 # Environment template
│   ├── .env                         # Local environment (git ignored)
│   ├── pyproject.toml               # Project metadata
│   └── setup.py                     # Package setup
├── frontend/                         # React + Electron frontend
│   ├── src/
│   │   ├── index.tsx
│   │   ├── main.tsx
│   │   ├── App.tsx
│   │   ├── components/              # Reusable components
│   │   │   ├── Layout/
│   │   │   ├── Forms/
│   │   │   ├── Charts/
│   │   │   └── Common/
│   │   ├── pages/                   # Page components
│   │   │   ├── Dashboard/
│   │   │   ├── Patients/
│   │   │   ├── EEGViewer/
│   │   │   └── Reports/
│   │   ├── hooks/                   # Custom React hooks
│   │   │   ├── useAuth.ts
│   │   │   ├── usePatients.ts
│   │   │   └── useEEG.ts
│   │   ├── services/                # API clients
│   │   │   ├── api.ts
│   │   │   ├── auth.ts
│   │   │   ├── patients.ts
│   │   │   └── eeg.ts
│   │   ├── store/                   # State management
│   │   │   ├── authStore.ts
│   │   │   ├── patientStore.ts
│   │   │   └── uiStore.ts
│   │   ├── types/                   # TypeScript types
│   │   │   ├── index.ts
│   │   │   ├── auth.ts
│   │   │   ├── patient.ts
│   │   │   └── eeg.ts
│   │   ├── styles/                  # Global styles
│   │   │   ├── globals.css
│   │   │   └── tailwind.css
│   │   └── utils/                   # Utility functions
│   │       ├── formatters.ts
│   │       ├── validators.ts
│   │       └── constants.ts
│   ├── public/                      # Static assets
│   ├── tests/                       # Frontend tests
│   │   ├── __tests__/
│   │   ├── setup.ts
│   │   └── jest.config.ts
│   ├── electron/                    # Electron main process
│   │   ├── main.ts
│   │   ├── preload.ts
│   │   └── ipc.ts
│   ├── vite.config.ts               # Vite configuration
│   ├── tsconfig.json                # TypeScript configuration
│   ├── package.json                 # Dependencies
│   ├── .env.example                 # Environment template
│   └── .env                         # Local environment
├── ai_engine/                        # AI processing pipeline
│   ├── __init__.py
│   ├── eeg_processor/               # EEG signal processing
│   │   ├── __init__.py
│   │   ├── loader.py                # Load EEG files
│   │   ├── preprocessor.py          # Preprocessing
│   │   ├── quality_analyzer.py      # Signal quality
│   │   └── artifact_detector.py     # Artifact detection
│   ├── models/                      # Machine learning models
│   │   ├── __init__.py
│   │   ├── base_model.py
│   │   ├── pattern_detector.py
│   │   └── models.onnx              # Pre-trained models
│   ├── feature_extraction/          # Feature engineering
│   │   ├── __init__.py
│   │   ├── features.py
│   │   └── frequency_domain.py
│   ├── explainability/              # Explainable AI
│   │   ├── __init__.py
│   │   ├── attention_maps.py
│   │   └── explanations.py
│   └── tests/                       # AI tests
│       ├── __init__.py
│       └── test_eeg_processor.py
├── infrastructure/                   # Infrastructure configs
│   ├── docker/
│   │   ├── Dockerfile.backend
│   │   ├── Dockerfile.frontend
│   │   └── Dockerfile.ai_engine
│   ├── k8s/                         # Kubernetes (future)
│   │   └── README.md
│   ├── nginx/
│   │   └── nginx.conf
│   └── scripts/
│       ├── init_db.sh
│       ├── migrate_db.sh
│       └── backup_db.sh
├── docs/                            # Documentation
│   ├── README.md
│   ├── SETUP.md
│   ├── ARCHITECTURE.md
│   ├── DEVELOPMENT.md
│   ├── TESTING.md
│   ├── SECURITY.md
│   ├── PRIVACY.md
│   ├── architecture/
│   │   ├── README.md
│   │   ├── CLEAN_ARCHITECTURE.md
│   │   ├── DDD.md
│   │   ├── DATABASE.md
│   │   └── ADRs/
│   ├── api/
│   │   ├── README.md
│   │   ├── AUTHENTICATION.md
│   │   ├── ENDPOINTS.md
│   │   └── ERROR_HANDLING.md
│   ├── deployment/
│   │   ├── README.md
│   │   ├── DOCKER.md
│   │   ├── CI_CD.md
│   │   └── KUBERNETES.md
│   ├── ai/
│   │   ├── README.md
│   │   ├── EEG_PROCESSING.md
│   │   ├── MODELS.md
│   │   └── FEATURE_EXTRACTION.md
│   ├── frontend/
│   │   ├── README.md
│   │   ├── COMPONENTS.md
│   │   └── STATE_MANAGEMENT.md
│   └── database/
│       ├── README.md
│       ├── SCHEMA.md
│       └── MIGRATIONS.md
├── .github/                         # GitHub configuration
│   ├── workflows/
│   │   ├── ci.yml
│   │   ├── test.yml
│   │   └── deploy.yml
│   ├── ISSUE_TEMPLATE/
│   │   ├── bug_report.md
│   │   └── feature_request.md
│   └── PULL_REQUEST_TEMPLATE/
│       └── pull_request_template.md
├── docker-compose.yml               # Local development
├── docker-compose.prod.yml          # Production
├── .env.example                     # Environment template
├── .editorconfig                    # Editor settings
├── .gitignore                       # Git ignore rules
├── README.md                        # Project README
├── LICENSE                          # MIT License
├── CONTRIBUTING.md                  # Contribution guide
└── CODE_OF_CONDUCT.md               # Code of conduct
```

## Data Flow

### Typical Request Flow

```
1. HTTP Request
   ↓
2. NGINX (Routing)
   ↓
3. FastAPI (Route Handler)
   ↓
4. Middleware (Auth, Logging)
   ↓
5. API Route Handler (Controller)
   ↓
6. Use Case (Orchestration)
   ↓
7. Domain Service (Business Logic)
   ↓
8. Repository (Data Access)
   ↓
9. Database (Storage)
   ↓
10. Response DTO
   ↓
11. HTTP Response
```

### Event-Driven Flow

```
1. Domain Event Triggered
   ↓
2. Event Published to Message Bus
   ↓
3. Event Handlers Subscribe
   ↓
4. Event Handlers Execute Side Effects
   ├→ Send Email
   ├→ Update Cache
   ├→ Trigger Async Jobs
   └→ Log Audit Trail
```

## Technology Separation

The architecture ensures technologies are separated:

- **Core Domain**: No framework dependencies
- **Use Cases**: No database dependencies
- **Interface Adapters**: No business logic
- **Infrastructure**: Abstracted behind interfaces

This allows:
- Easy testing (mock any layer)
- Technology swaps (e.g., PostgreSQL → MongoDB)
- Framework independence
- Clear responsibility separation

## Scalability Path

### Phase 1: Modular Monolith (Current)
- Single deployment
- All features in one codebase
- Shared database

### Phase 2: Distributed Monolith
- Multiple instances
- Load balancing
- Caching layer
- Message queue

### Phase 3: Microservices
- Separate services per bounded context
- Service-to-service communication
- Distributed database
- API Gateway

## Performance Considerations

- **Caching**: Redis for frequently accessed data
- **Async Processing**: Celery for long-running tasks
- **Database Indexing**: Optimized indices on frequent queries
- **API Pagination**: Cursor-based pagination for large datasets
- **Frontend**: Code splitting, lazy loading, CDN for assets

## Security Architecture

- **Authentication**: JWT with refresh tokens
- **Authorization**: RBAC with fine-grained permissions
- **Encryption**: AES-256 for sensitive data
- **API Security**: CORS, rate limiting, input validation
- **Database**: Parameterized queries, connection pooling
- **Audit**: Full audit logging of all actions

---

**Next**: Read [Clean Architecture Details](./architecture/CLEAN_ARCHITECTURE.md)
