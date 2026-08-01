<p align="center">
  <h1 align="center">🎯 CVnokta — AI-Powered Recruitment Platform</h1>
  <p align="center">
    <strong>Intelligent end-to-end hiring pipeline that automates CV screening, aptitude testing, AI interviews, and candidate ranking.</strong>
  </p>
  <p align="center">
    <a href="#-features">Features</a> •
    <a href="#-architecture">Architecture</a> •
    <a href="#-tech-stack">Tech Stack</a> •
    <a href="#-getting-started">Getting Started</a> •
    <a href="#-api-reference">API Reference</a> •
    <a href="#-project-structure">Project Structure</a> •
    <a href="#-testing">Testing</a> •
    <a href="#-deployment">Deployment</a>
  </p>
</p>

---

## 📌 About the Project

**CVnokta** is a graduation capstone project that reimagines the recruitment process by integrating artificial intelligence into every stage of the hiring pipeline. Traditional HR workflows suffer from manual CV reviews, inconsistent candidate evaluation, subjective interview scoring, and a time-consuming, error-prone process that does not scale. CVnokta solves these problems by providing an **automated, AI-driven recruitment platform** where companies can post jobs, receive applications, and let the system intelligently evaluate candidates from CV submission to final ranking — all with minimal human intervention.

### 🔍 The Problem

| Challenge | Impact |
|---|---|
| **Manual CV Screening** | HR teams spend 23+ hours reviewing resumes for a single position |
| **Inconsistent Evaluation** | Different reviewers produce different assessments for the same candidate |
| **Subjective Interviews** | Unstructured interviews lead to biased hiring decisions |
| **Slow Feedback Loops** | Candidates wait weeks for a response, damaging employer brand |
| **No Scalability** | Process breaks down when application volume spikes |

### ✅ Our Solution

CVnokta introduces a **fully automated, multi-stage recruitment pipeline** that:

1. **Analyzes CVs with RAG** — Uses Retrieval-Augmented Generation (RAG) with FAISS vector search and GPT-4o-mini to objectively score resumes against job requirements
2. **Generates tailored assessments** — AI creates role-specific technical tests and English proficiency exams dynamically
3. **Conducts AI voice interviews** — Real-time voice interviews powered by OpenAI's Realtime API with an AI interviewer that adapts questions based on candidate responses
4. **Ranks candidates objectively** — Weighted multi-criteria scoring across all pipeline stages produces a fair, transparent ranking
5. **Provides dual-perspective feedback** — AI-generated feedback for both HR teams and candidates after every stage

---

## ✨ Features

### 🏢 Company (HR) Side
- **Job Posting Management** — Create, edit, publish, and archive job postings with AI-assisted requirement generation
- **Automated Pipeline** — Once a candidate applies, the system autonomously drives them through each evaluation stage
- **Smart Dashboard** — Real-time analytics, application statistics, and pipeline health monitoring
- **Candidate Management** — View applicants, track stage progress, see detailed evaluation scores
- **Best Candidates View** — AI-ranked leaderboard with weighted composite scores
- **Stage Feedback** — Dual-perspective AI feedback (HR + Candidate) for every evaluation stage

### 👤 Candidate Side
- **Job Discovery & Application** — Browse openings and apply with CV upload
- **Online Examinations** — Take AI-generated technical and English tests via secure, token-authenticated links
- **AI Voice Interview** — Real-time voice interview with an AI interviewer powered by OpenAI Realtime API
- **Application Tracking** — Monitor application status across pipeline stages
- **Profile Management** — Manage personal info, view application history, and see feedback

### 🤖 AI / NLP Capabilities
- **CV Analysis (RAG Pipeline)** — PDF parsing → text cleaning → embedding → FAISS similarity search → GPT scoring
- **Test Generation** — Dynamic technical & English questions tailored to job requirements using GPT
- **Realtime Voice Interview** — WebSocket-based voice sessions with OpenAI Realtime API (TTS + STT + conversation)
- **Candidate Ranking** — Multi-criteria weighted evaluation engine
- **AI Feedback Generation** — Dual-perspective (HR/Candidate) natural language feedback
- **Chatbot** — AI-powered chatbot for candidate Q&A
- **Performance Benchmarking** — Built-in latency tracking and pipeline benchmarking

---

## 🏗 Architecture

The platform follows a **microservices architecture** with three independently deployable services:

```
┌──────────────────────────────────────────────────────────────┐
│                     FRONTEND (React)                         │
│         Vercel / Static Hosting — Port 3000                  │
│                                                              │
│   ┌───────────┐ ┌───────────┐ ┌─────────┐ ┌──────────────┐  │
│   │ Job List  │ │ Company   │ │  Exam   │ │   AI Voice   │  │
│   │ & Apply   │ │  Panel    │ │  Page   │ │  Interview   │  │
│   └─────┬─────┘ └─────┬─────┘ └────┬────┘ └──────┬───────┘  │
│         │             │            │              │          │
└─────────┼─────────────┼────────────┼──────────────┼──────────┘
          │ REST        │ REST       │ REST         │ WebSocket
          ▼             ▼            ▼              ▼
┌──────────────────────────────────────────────────────────────┐
│                BACKEND (.NET 8 Web API)                       │
│        Azure App Service / Docker — Port 8080                │
│                                                              │
│   Clean Architecture (Core → Application → Infrastructure)   │
│                                                              │
│   ┌────────────┐ ┌──────────┐ ┌──────────┐ ┌─────────────┐  │
│   │ Account &  │ │  Job     │ │  Exam &  │ │  Pipeline   │  │
│   │   Auth     │ │ Postings │ │  Tests   │ │   Engine    │  │
│   └────────────┘ └──────────┘ └──────────┘ └──────┬──────┘  │
│                                                    │         │
│   SQL Server (Azure SQL)    JWT Auth    Serilog    │         │
└────────────────────────────────────────────────────┼─────────┘
                                                     │ HTTP
                                                     ▼
┌──────────────────────────────────────────────────────────────┐
│              AI-NLP SERVICE (FastAPI / Python)                │
│         Hugging Face Spaces / Docker — Port 7860             │
│                                                              │
│   ┌──────────┐ ┌──────────┐ ┌───────────┐ ┌──────────────┐  │
│   │ CV RAG   │ │  Test    │ │ Realtime  │ │  Ranking &   │  │
│   │ Analysis │ │ Engine   │ │ Interview │ │  Feedback    │  │
│   └──────────┘ └──────────┘ └───────────┘ └──────────────┘  │
│                                                              │
│   OpenAI GPT-4o-mini   FAISS   Sentence-Transformers        │
│   LangChain   Whisper   TTS    OpenAI Realtime API           │
└──────────────────────────────────────────────────────────────┘
```

### Backend — Clean Architecture

The .NET backend follows **Clean Architecture** with strict dependency inversion:

| Layer | Purpose | Key Contents |
|-------|---------|-------------|
| **Core (Application)** | Business logic, entities, CQRS | Entities, Features (Commands/Queries), DTOs, Interfaces, Validators |
| **Infrastructure** | Data access, external services | EF Core DbContext, Repositories, JWT Auth, Email, Pipeline Service |
| **WebApi** | HTTP surface, middleware | Controllers (v1), Swagger, Error Handling, API Versioning |

---

## 🛠 Tech Stack

### Backend
| Technology | Purpose |
|---|---|
| **.NET 8** | Web API framework |
| **C#** | Primary language |
| **Entity Framework Core 8** | ORM & database migrations |
| **SQL Server (Azure SQL)** | Relational database |
| **ASP.NET Identity** | Authentication & user management |
| **JWT Bearer Tokens** | Stateless authentication |
| **MediatR** | CQRS & Mediator pattern |
| **AutoMapper** | Object-to-object mapping |
| **FluentValidation** | Request validation |
| **Serilog** | Structured logging |
| **Swashbuckle** | OpenAPI / Swagger documentation |
| **Resend** | Transactional email delivery |
| **Docker** | Containerization |

### Frontend
| Technology | Purpose |
|---|---|
| **React 19** | UI library |
| **React Router v7** | Client-side routing |
| **Axios** | HTTP client |
| **Recharts** | Data visualization / charts |
| **PDF.js** | Client-side PDF rendering |
| **CSS Modules** | Component-scoped styling |

### AI-NLP Service
| Technology | Purpose |
|---|---|
| **Python 3.10+** | Primary language |
| **FastAPI** | Async API framework |
| **OpenAI GPT-4o-mini** | LLM for analysis, test generation, scoring |
| **OpenAI Realtime API** | Real-time voice interview (WebSocket) |
| **OpenAI Whisper** | Speech-to-text transcription |
| **OpenAI TTS** | Text-to-speech voice synthesis |
| **LangChain** | LLM orchestration framework |
| **FAISS** | Vector similarity search |
| **Sentence-Transformers** | Multilingual text embeddings |
| **PyMuPDF / pdfplumber** | PDF text extraction |
| **Streamlit** | Demo UI for standalone testing |
| **Pydantic v2** | Data validation & settings |
| **Docker** | Containerization |

### Infrastructure & DevOps
| Technology | Purpose |
|---|---|
| **Azure App Service** | Backend hosting |
| **Azure SQL Database** | Production database |
| **Hugging Face Spaces** | AI-NLP service hosting |
| **Vercel** | Frontend hosting |
| **Docker** | Container images for all services |

---

## 📁 Project Structure

```
CVnokta-graduation-project/
├── Backend/                           # .NET 8 Web API (Clean Architecture)
│   ├── CleanArchitecture.sln          # Solution file
│   ├── Dockerfile                     # Backend container image
│   ├── CleanArchitecture/
│   │   ├── CleanArchitecture.Application/   # Core: Entities, Features (CQRS), DTOs, Interfaces
│   │   │   ├── Entities/              # 30 domain entities (JobPosting, CvAnalysisResult, Exam, etc.)
│   │   │   ├── Features/             # CQRS Commands & Queries grouped by domain
│   │   │   │   ├── Applications/     # Job application management
│   │   │   │   ├── CandidateExams/   # Exam assignment & submission
│   │   │   │   ├── Evaluations/      # Final evaluation scoring
│   │   │   │   ├── Exams/            # Exam CRUD & question management
│   │   │   │   ├── Interviews/       # AI interview session management
│   │   │   │   ├── JobPostings/      # Job posting lifecycle
│   │   │   │   ├── MarketStats/      # Market intelligence
│   │   │   │   ├── Meetings/         # Meeting invitations
│   │   │   │   ├── VideoInterviews/  # Video interview tracking
│   │   │   │   └── ...
│   │   │   ├── Interfaces/           # Repository & service contracts
│   │   │   ├── DTOs/                 # Data Transfer Objects
│   │   │   ├── Mappings/            # AutoMapper profiles
│   │   │   └── Wrappers/            # Response wrappers (PagedResponse, etc.)
│   │   │
│   │   ├── CleanArchitecture.Infrastructure/  # Data access & external services
│   │   │   ├── Contexts/             # EF Core DbContext (30+ entity configurations)
│   │   │   ├── Repositories/         # Generic repository pattern
│   │   │   ├── Services/             # AccountService, PipelineService, EmailService, etc.
│   │   │   ├── Migrations/           # Database migration history
│   │   │   └── Seeds/                # Default roles, admin user, sample data
│   │   │
│   │   └── CleanArchitecture.WebApi/  # API surface layer
│   │       ├── Controllers/
│   │       │   ├── AccountController.cs        # Auth (register, login, JWT)
│   │       │   └── v1/
│   │       │       ├── JobPostingsController.cs    # Job CRUD + AI integration
│   │       │       ├── ApplicationsController.cs   # Application lifecycle
│   │       │       ├── ExamController.cs            # Exam assignment & grading
│   │       │       ├── InterviewsController.cs      # AI interview management
│   │       │       ├── CvAnalysisController.cs      # CV analysis proxy to AI service
│   │       │       ├── EvaluationsController.cs     # Final evaluation scores
│   │       │       ├── PipelineController.cs        # Pipeline stage transitions
│   │       │       ├── StatisticsController.cs      # Dashboard analytics
│   │       │       └── ...
│   │       ├── Middlewares/           # Error handling, request logging
│   │       ├── Extensions/            # Swagger, API versioning setup
│   │       └── Program.cs             # Application entry point
│   │
│   └── Tests/                         # Backend test projects
│       ├── CleanArchitecture.UnitTests/
│       └── CleanArchitecture.Infrastructure.Tests/
│
├── AI-NLP/                            # Python AI/NLP microservice
│   ├── Dockerfile                     # AI service container image
│   ├── requirements.txt               # Python dependencies
│   ├── .env.example                   # Environment variable template
│   ├── app/
│   │   ├── main.py                    # FastAPI entry point, router registration
│   │   ├── config.py                  # Centralized settings (pydantic-settings)
│   │   ├── middleware.py              # API key auth, request tracking
│   │   ├── api/v1/                    # API endpoint routers
│   │   │   ├── cv_analysis.py         # CV upload & RAG scoring endpoints
│   │   │   ├── tests.py               # Test generation & grading endpoints
│   │   │   ├── realtime_interview.py  # WebSocket realtime voice interview
│   │   │   ├── rankings.py            # Candidate ranking endpoints
│   │   │   ├── feedback.py            # Dual-perspective feedback generation
│   │   │   ├── chatbot.py             # AI chatbot endpoints
│   │   │   └── backend_integration.py # Backend ↔ AI sync endpoints
│   │   ├── core/                      # Business logic engines
│   │   │   ├── cv_parser.py           # PDF text extraction & section parsing
│   │   │   ├── cv_scorer.py           # RAG pipeline: embed → search → GPT score
│   │   │   ├── test_engine.py         # Dynamic test question generation
│   │   │   ├── ranking_engine.py      # Weighted multi-criteria ranking
│   │   │   ├── feedback_engine.py     # AI feedback generation (HR + Candidate)
│   │   │   ├── realtime_engine.py     # OpenAI Realtime API WebSocket proxy
│   │   │   └── prompts/               # Versioned prompt templates
│   │   ├── services/                  # External service integrations
│   │   │   ├── openai_service.py      # OpenAI API client with caching
│   │   │   ├── embedding_service.py   # Sentence-Transformers embeddings
│   │   │   ├── vector_store.py        # FAISS index management
│   │   │   ├── speech_service.py      # TTS/STT via OpenAI
│   │   │   └── backend_client.py      # HTTP client for Backend API
│   │   ├── models/                    # Pydantic data models
│   │   └── utils/                     # Text cleaning, logging, helpers
│   └── tests/                         # 18 test modules (pytest)
│       ├── test_api.py                # API integration tests
│       ├── test_cv_parser.py          # CV parsing unit tests
│       ├── test_cv_scorer.py          # Scoring accuracy tests
│       ├── test_realtime_engine.py    # Realtime interview engine tests
│       ├── test_performance.py        # Latency benchmarks
│       └── ...
│
├── Frontend/                          # React 19 SPA
│   ├── package.json                   # npm dependencies
│   ├── src/
│   │   ├── App.js                     # Root component & routing
│   │   ├── Components/               # Shared UI components (Header, Footer, Body)
│   │   └── Pages/                    # Page-level components
│   │       ├── Mainpage/              # Landing page
│   │       ├── Login/ & Signup/       # Authentication pages
│   │       ├── JobList/ & JobView/    # Job discovery
│   │       ├── ApplicationForm/       # Job application with CV upload
│   │       ├── CompanyPanel/          # HR dashboard, job management, candidates
│   │       │   ├── Dashboard/         # Analytics & statistics
│   │       │   ├── Jobs/              # Job listing management
│   │       │   ├── CreateJob/         # AI-assisted job creation
│   │       │   ├── Candidates/        # Applicant tracking
│   │       │   └── BestCandidates/    # AI-ranked candidate leaderboard
│   │       ├── UserProfile/           # Candidate dashboard & profile
│   │       ├── Exam/                  # Online exam taking interface
│   │       ├── VideoInterview/        # AI voice interview room
│   │       └── Insights/             # Market insights & analytics
│   └── public/                        # Static assets
│
└── About Project/                     # Academic documentation
    ├── Project Summary.pdf
    └── CSE392_Tubitak2209_*.pdf       # TÜBİTAK 2209-A project proposal
```

---

## 🚀 Getting Started

### Prerequisites

| Tool | Version | Purpose |
|------|---------|---------|
| **.NET SDK** | 8.0+ | Backend development |
| **Node.js** | 18+ | Frontend development |
| **Python** | 3.10+ | AI-NLP service |
| **SQL Server** | Any | Database (or use InMemory mode) |
| **Docker** | Latest | Containerized deployment (optional) |
| **OpenAI API Key** | — | AI features (GPT, Whisper, TTS, Realtime) |

### 1️⃣ Clone the Repository

```bash
git clone https://github.com/your-username/CVnokta-graduation-project.git
cd CVnokta-graduation-project
```

### 2️⃣ Backend Setup (.NET 8)

```bash
cd Backend

# Restore NuGet packages
dotnet restore CleanArchitecture.sln

# Update connection string in appsettings.json (or use environment variables)
# For local development, you can use InMemory database:
# Set "UseInMemoryDatabase": true in appsettings.json

# Apply database migrations (if using SQL Server)
cd CleanArchitecture/CleanArchitecture.WebApi
dotnet ef database update

# Run the backend
dotnet run
# API will be available at https://localhost:9001
# Swagger UI at https://localhost:9001/swagger
```

**Key environment variables:**
| Variable | Description |
|---|---|
| `ConnectionStrings__DefaultConnection` | SQL Server connection string |
| `JWTSettings__Key` | JWT signing key |
| `AiSettings__BaseUrl` | AI-NLP service URL |
| `AiSettings__ApiKey` | API key for AI-NLP service |
| `ResendSettings__ApiKey` | Resend email API key |

### 3️⃣ AI-NLP Service Setup (Python / FastAPI)

```bash
cd AI-NLP

# Create virtual environment
python -m venv venv
source venv/bin/activate    # Linux/Mac
venv\Scripts\activate       # Windows

# Install dependencies
pip install -r requirements.txt

# Configure environment
cp .env.example .env
# Edit .env and set your OPENAI_API_KEY

# Run the service
uvicorn app.main:app --reload --port 8000
# API docs at http://localhost:8000/docs
# Health check at http://localhost:8000/health
```

**Key environment variables:**
| Variable | Description |
|---|---|
| `OPENAI_API_KEY` | **Required** — OpenAI API key for GPT, Whisper, TTS |
| `OPENAI_MODEL` | LLM model (default: `gpt-4o-mini`) |
| `EMBEDDING_MODEL` | Sentence-Transformers model (default: `paraphrase-multilingual-MiniLM-L12-v2`) |
| `AI_NLP_API_KEY` | Inbound API key for Backend → AI communication |
| `CV_PASS_THRESHOLD` | Minimum CV score to pass (default: `85`) |
| `USE_MOCK_DATA` | Use mock data for testing (default: `true`) |

### 4️⃣ Frontend Setup (React)

```bash
cd Frontend

# Install dependencies
npm install

# Configure API endpoints
# Edit .env.development:
# REACT_APP_API_BASE_URL=https://localhost:9001
# REACT_APP_AI_NLP_BASE_URL=http://localhost:8000

# Start development server
npm start
# App will open at http://localhost:3000
```

### 5️⃣ Docker Deployment

**Backend:**
```bash
cd Backend
docker build -t cvnokta-backend .
docker run -p 8080:8080 \
  -e "ConnectionStrings__DefaultConnection=your_connection_string" \
  -e "JWTSettings__Key=your_jwt_key" \
  cvnokta-backend
```

**AI-NLP Service:**
```bash
cd AI-NLP
docker build -t cvnokta-ai .
docker run -p 7860:7860 \
  -e "OPENAI_API_KEY=your_openai_key" \
  cvnokta-ai
```

---

## 📡 API Reference

### Backend API (REST)

| Endpoint | Method | Description |
|---|---|---|
| `/api/v1/Account/authenticate` | POST | Login & receive JWT token |
| `/api/v1/Account/register` | POST | Register new user |
| `/api/v1/JobPostings` | GET/POST | List & create job postings |
| `/api/v1/JobPostings/{id}` | GET/PUT/DELETE | Job posting CRUD |
| `/api/v1/Applications` | GET/POST | List & submit applications |
| `/api/v1/Applications/{id}/pipeline` | GET | Get pipeline stage status |
| `/api/v1/Exam/assignments/{token}` | GET | Get exam by secure token |
| `/api/v1/Exam/submit` | POST | Submit exam answers |
| `/api/v1/Interviews` | GET/POST | AI interview session management |
| `/api/v1/CvAnalysis` | POST | Trigger CV analysis via AI service |
| `/api/v1/Evaluations` | GET | Get final evaluation scores |
| `/api/v1/Statistics` | GET | Dashboard analytics |
| `/api/v1/Pipeline/advance` | POST | Manual pipeline stage advance |
| `/health` | GET | Health check endpoint |

### AI-NLP API (REST + WebSocket)

| Endpoint | Method | Description |
|---|---|---|
| `/api/v1/cv/analyze` | POST | Analyze CV against job posting (RAG) |
| `/api/v1/cv/parse` | POST | Parse CV and extract sections |
| `/api/v1/tests/generate` | POST | Generate technical/English test questions |
| `/api/v1/tests/evaluate` | POST | Grade submitted test answers |
| `/api/v1/interview/realtime/session` | POST | Create realtime interview session |
| `/api/v1/interview/realtime/ws/{session_id}` | WS | WebSocket voice interview connection |
| `/api/v1/rankings/evaluate` | POST | Compute weighted candidate ranking |
| `/api/v1/feedback/generate` | POST | Generate dual-perspective stage feedback |
| `/api/v1/chatbot/ask` | POST | AI chatbot Q&A |
| `/health` | GET | Service health & status |
| `/config` | GET | Current service configuration |
| `/metrics/latency` | GET | Per-stage latency statistics |
| `/benchmark` | POST | Run fallback pipeline benchmark |

---

## 🔄 Recruitment Pipeline Flow

The automated pipeline processes each candidate through the following stages:

```
┌──────────┐     ┌──────────────┐     ┌────────────────┐     ┌───────────────┐     ┌──────────────┐
│  Apply   │────▸│  CV Analysis │────▸│  Technical     │────▸│   English     │────▸│  AI Voice    │
│  (Upload │     │  (RAG + GPT) │     │  Assessment    │     │   Proficiency │     │  Interview   │
│   CV)    │     │  Score ≥ 85  │     │  AI-Generated  │     │   B1-B2 Level │     │  (Realtime)  │
└──────────┘     └──────┬───────┘     └───────┬────────┘     └──────┬────────┘     └──────┬───────┘
                        │                     │                     │                     │
                   Pass / Fail           Pass / Fail           Pass / Fail           Scored
                        │                     │                     │                     │
                        ▼                     ▼                     ▼                     ▼
                  ┌──────────────────────────────────────────────────────────────────────────┐
                  │                    FINAL RANKING ENGINE                                   │
                  │                                                                          │
                  │   CV Score (20%) + Tech Test (25%) + English (25%) + Interview (30%)      │
                  │                                                                          │
                  │   → Weighted composite score → Ranked candidate list                     │
                  └──────────────────────────────────────────────────────────────────────────┘
```

**Each stage is fully automated:**
- When a candidate completes a stage, the **PipelineService** evaluates their score
- If they pass, the system **automatically advances them** to the next stage and sends email notifications
- If they fail, the application is **rejected with AI-generated feedback**
- HR can also manually advance or reject candidates via the dashboard

---

## 🧪 Testing

### Backend Tests

```bash
cd Backend
dotnet test CleanArchitecture.sln
```

The backend includes:
- **Unit Tests** — Business logic, validators, mapping profiles
- **Infrastructure Tests** — Repository patterns, service layer

### AI-NLP Tests

```bash
cd AI-NLP

# Run all tests
pytest tests/ -v

# Run specific test modules
pytest tests/test_cv_parser.py -v          # CV parsing
pytest tests/test_cv_scorer.py -v          # Scoring accuracy
pytest tests/test_realtime_engine.py -v    # Realtime interview
pytest tests/test_performance.py -v        # Latency benchmarks
pytest tests/test_edge_cases.py -v         # Edge case handling
```

The AI-NLP service has **18 comprehensive test modules** covering:
- API integration tests
- CV parsing & scoring accuracy
- Realtime interview engine (session lifecycle, WebSocket handling)
- Ranking engine edge cases
- Performance benchmarks & threshold calibration
- Failure modes & regression tests

### Frontend Tests

```bash
cd Frontend
npm test
```

---

## 🌐 Deployment

| Service | Platform | URL |
|---|---|---|
| **Frontend** | Vercel | `https://c-vnokta-graduation-project.vercel.app` |
| **Backend** | Azure App Service | Dockerized .NET 8 API |
| **AI-NLP** | Hugging Face Spaces | `https://ozger0202-cvnokta-ai.hf.space` |
| **Database** | Azure SQL | Managed SQL Server |

---

## 🔐 Security

- **JWT Authentication** — Stateless token-based auth with role claims (SuperAdmin, Basic)
- **API Key Protection** — Backend ↔ AI-NLP communication secured with API keys
- **Secure Exam Tokens** — One-time-use signed tokens for exam & interview access
- **Password Reset** — Email-based password reset flow via Resend
- **CORS Configuration** — Strict origin allowlisting
- **Input Validation** — FluentValidation (Backend) + Pydantic (AI-NLP)
- **PII Masking** — Personal information masked before AI processing

---

## 🤝 Team & Roles

This is a **graduation capstone project** developed by a team of university students. My role was:

- **Team Lead** — Project planning, architecture decisions, task distribution, sprint management
- **Backend Developer (Primary)** — Designed and implemented the entire .NET 8 backend with Clean Architecture, including the automated pipeline engine, JWT authentication, CQRS features, and all API controllers
- **AI/NLP Support** — Contributed to the AI-NLP microservice development, particularly backend integration, pipeline coordination, and testing

---

## 📄 Academic Context

This project was developed as part of the **CSE392 Graduation Project** course and was submitted as a **TÜBİTAK 2209-A** undergraduate research project proposal titled:

> **"AI-Supported Virtual Human Resources Expert"** *(Yapay Zeka Destekli Sanal İnsan Kaynakları Uzmanı)*

The project demonstrates the practical application of:
- Large Language Models (LLMs) in enterprise workflows
- Retrieval-Augmented Generation (RAG) for document analysis
- Real-time voice AI systems
- Microservices architecture with Clean Architecture patterns
- Full-stack development with modern web technologies

---

## 📜 License

This project was developed for academic purposes as a graduation capstone project.

---

<p align="center">
  <sub>Built with ❤️ by the CVnokta Team — 2026</sub>
</p>
