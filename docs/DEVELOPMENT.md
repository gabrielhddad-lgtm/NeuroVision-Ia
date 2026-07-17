# NeuroVision AI - Development Guide

## Development Workflow

### 1. Getting Started

1. Fork the repository
2. Clone your fork locally
3. Create a feature branch from `develop`
4. Set up development environment (see [Setup Guide](./SETUP.md))
5. Make changes following coding standards
6. Push to your branch
7. Create a Pull Request

### 2. Branch Strategy

```
main (production releases)
  ↑
develop (integration branch)
  ↑
feature/* (feature branches)
bugfix/* (bug fix branches)
refactor/* (refactoring branches)
```

### 3. Code Organization

#### Backend Structure

```
backend/app/
├── api/              # HTTP route handlers
├── core/             # Configuration and exceptions
├── domain/           # DDD domain layer
├── application/      # Use cases and services
���── infrastructure/   # Database, external services
└── shared/           # Middleware, schemas, utils
```

#### Frontend Structure

```
frontend/src/
├── components/       # Reusable components
├── pages/            # Page components
├── hooks/            # Custom hooks
├── services/         # API clients
├── store/            # State management
├── types/            # TypeScript types
├── styles/           # Global styles
└── utils/            # Utilities
```

### 4. Coding Standards

#### Python Backend

**PEP 8 Compliance**:

```python
# Good: Clear naming, docstrings, type hints
def create_patient(
    first_name: str,
    last_name: str,
    date_of_birth: date,
) -> Patient:
    """
    Create a new patient record.
    
    Args:
        first_name: Patient's first name
        last_name: Patient's last name
        date_of_birth: Patient's date of birth
    
    Returns:
        Created Patient entity
    
    Raises:
        InvalidPatientDataError: If provided data is invalid
    """
    if not first_name or not last_name:
        raise InvalidPatientDataError("Name cannot be empty")
    
    patient = Patient(
        first_name=first_name,
        last_name=last_name,
        date_of_birth=date_of_birth,
    )
    return patient
```

**File Organization**:

```python
# Imports (organized in 3 groups)
from datetime import date
from typing import Optional

from fastapi import APIRouter
from pydantic import BaseModel

from app.domain.entities import Patient
from app.infrastructure.database import SessionLocal


# Constants
PATIENT_NAME_MAX_LENGTH = 100
PATIENT_NAME_MIN_LENGTH = 2


# Models/Schemas
class PatientRequest(BaseModel):
    first_name: str
    last_name: str


# Functions/Classes
def process_patient(patient: Patient) -> None:
    pass
```

**Type Hints**:

```python
# Use type hints everywhere
from typing import Optional, List, Dict, Tuple

def get_patients(
    limit: int = 10,
    offset: int = 0,
) -> List[Patient]:
    pass

def search_patients(query: str) -> Optional[List[Patient]]:
    pass

def patient_to_dict(patient: Patient) -> Dict[str, str]:
    pass
```

#### TypeScript Frontend

**File Organization**:

```typescript
// Imports
import React from 'react';
import { useQuery } from '@tanstack/react-query';

import { PatientService } from '@/services/patients';
import { Button } from '@/components/Button';
import type { Patient } from '@/types';

// Constants
const PATIENT_PAGE_SIZE = 20;

// Component
interface PatientListProps {
  onSelect?: (patient: Patient) => void;
}

export const PatientList: React.FC<PatientListProps> = ({ onSelect }) => {
  const { data, isLoading } = useQuery({
    queryKey: ['patients'],
    queryFn: () => PatientService.list(),
  });

  return (
    <div className="space-y-4">
      {/* Component JSX */}
    </div>
  );
};
```

**Type Safety**:

```typescript
// Use strict types
interface User {
  id: string;
  email: string;
  role: 'admin' | 'physician' | 'technician';
}

type UserRole = 'admin' | 'physician' | 'technician';

const hasPermission = (user: User, action: string): boolean => {
  // Type-safe code
  return true;
};
```

### 5. Commit Standards

Use conventional commits:

```
<type>(<scope>): <subject>

<body>

<footer>
```

**Examples**:

```
feat(auth): add JWT refresh token rotation

Implement automatic refresh token rotation for enhanced security.
Tokens are rotated on each refresh request.

Fixes #123

---

feat(patient): add patient search functionality

Add full-text search on patient names and medical record numbers.
Implements cursor-based pagination for large result sets.

Fixes #456
Related-to: #789

---

fix(eeg-viewer): correct signal scaling calculation

Fix issue where EEG signal scaling was incorrect for certain
frequency ranges. Scaling now properly accounts for all frequency
bands.

Fixes #321
```

### 6. Testing Standards

#### Backend (pytest)

```python
# tests/unit/application/test_patient_service.py
import pytest
from app.application.services import PatientService
from app.domain.entities import Patient
from app.core.exceptions import PatientNotFoundError


class TestPatientService:
    """Test PatientService."""

    @pytest.fixture
    def patient_service(self):
        """Create patient service instance."""
        return PatientService()

    def test_create_patient_success(self, patient_service):
        """Test successful patient creation."""
        patient = patient_service.create_patient(
            first_name="John",
            last_name="Doe",
        )
        assert patient.first_name == "John"
        assert patient.last_name == "Doe"

    def test_create_patient_invalid_name(self, patient_service):
        """Test patient creation with invalid name."""
        with pytest.raises(ValueError):
            patient_service.create_patient(
                first_name="",
                last_name="Doe",
            )

    def test_get_patient_not_found(self, patient_service):
        """Test getting non-existent patient."""
        with pytest.raises(PatientNotFoundError):
            patient_service.get_patient(999)
```

#### Frontend (Jest)

```typescript
// src/components/__tests__/PatientList.test.tsx
import { render, screen } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { QueryClient, QueryClientProvider } from '@tanstack/react-query';

import { PatientList } from '../PatientList';

describe('PatientList', () => {
  const queryClient = new QueryClient({
    defaultOptions: {
      queries: { retry: false },
    },
  });

  it('should render patient list', () => {
    render(
      <QueryClientProvider client={queryClient}>
        <PatientList />
      </QueryClientProvider>
    );

    expect(screen.getByText(/patients/i)).toBeInTheDocument();
  });

  it('should call onSelect when patient clicked', async () => {
    const onSelect = jest.fn();
    const user = userEvent.setup();

    render(
      <QueryClientProvider client={queryClient}>
        <PatientList onSelect={onSelect} />
      </QueryClientProvider>
    );

    const patient = screen.getByRole('button', { name: /john doe/i });
    await user.click(patient);

    expect(onSelect).toHaveBeenCalled();
  });
});
```

### 7. Error Handling

#### Backend

```python
from app.core.exceptions import (
    ApplicationError,
    ValidationError,
    NotFoundError,
    UnauthorizedError,
    ForbiddenError,
)


class PatientNotFoundError(NotFoundError):
    """Raised when patient is not found."""
    pass


def get_patient(patient_id: int) -> Patient:
    patient = repository.get(patient_id)
    if not patient:
        raise PatientNotFoundError(f"Patient {patient_id} not found")
    return patient
```

#### Frontend

```typescript
class APIError extends Error {
  constructor(
    public status: number,
    public code: string,
    message: string,
  ) {
    super(message);
  }
}

const handleError = (error: unknown) => {
  if (error instanceof APIError) {
    switch (error.status) {
      case 404:
        console.error('Not found:', error.message);
        break;
      case 401:
        console.error('Unauthorized');
        // Redirect to login
        break;
      default:
        console.error('Error:', error.message);
    }
  }
};
```

### 8. Code Review Checklist

Before submitting a PR, ensure:

- [ ] Code follows project standards
- [ ] Type hints on all functions
- [ ] Docstrings on public methods
- [ ] Tests written (>80% coverage)
- [ ] All tests passing
- [ ] No hardcoded secrets
- [ ] No console logs left
- [ ] Error handling implemented
- [ ] Documentation updated
- [ ] No breaking changes
- [ ] Commit messages follow conventions

### 9. Documentation

**Docstring Example (Python)**:

```python
def analyze_eeg_signal(
    signal: np.ndarray,
    sampling_rate: int,
    band: str = 'alpha',
) -> Dict[str, float]:
    """
    Analyze EEG signal for specified frequency band.
    
    Computes power spectral density and related metrics for the given
    frequency band. Uses Welch's method for PSD estimation.
    
    Args:
        signal: EEG signal as numpy array of shape (n_samples,)
        sampling_rate: Sampling rate in Hz
        band: Frequency band to analyze ('delta', 'theta', 'alpha', 'beta')
    
    Returns:
        Dictionary containing:
        - 'power': Power in the specified band
        - 'peak_frequency': Peak frequency in the band
        - 'mean_frequency': Mean frequency in the band
    
    Raises:
        ValueError: If band is not recognized
        TypeError: If signal is not 1D array
    
    Example:
        >>> signal = np.random.randn(1000)
        >>> metrics = analyze_eeg_signal(signal, 250, 'alpha')
        >>> print(metrics['power'])
    """
    pass
```

**Comment Example**:

```python
# Bad: Obvious comment
x = x + 1  # Increment x

# Good: Explains why
# Reset counter when threshold exceeded to prevent overflow
if counter > THRESHOLD:
    counter = 0
```

### 10. Local Testing

```bash
# Backend
cd backend

# Run all tests
pytest

# Run with coverage
pytest --cov=app tests/

# Run specific test
pytest tests/unit/test_auth.py

# Frontend
cd frontend

# Run tests
npm test

# Run with coverage
npm test -- --coverage

# Run specific test
npm test -- PatientList.test.tsx
```

### 11. Git Workflow

```bash
# Create feature branch
git checkout -b feature/patient-search

# Make changes and commit
git add .
git commit -m "feat(patient): add search functionality"

# Keep branch updated
git fetch origin
git rebase origin/develop

# Push to remote
git push origin feature/patient-search

# Create Pull Request on GitHub
# Request review from team members
# Address review comments
# Merge when approved

# Clean up
git checkout develop
git pull origin develop
git branch -d feature/patient-search
```

---

**Next**: Check [Testing Guide](./TESTING.md)
