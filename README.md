<div align="center">
  
# WorkWise

**AI-Native Career Intelligence & Telemetry Platform**

[![Next.js](https://img.shields.io/badge/Next.js-15.2-black?style=for-the-badge&logo=next.js)](https://nextjs.org/)
[![React](https://img.shields.io/badge/React-19.0-20232A?style=for-the-badge&logo=react)](https://reactjs.org/)
[![Prisma](https://img.shields.io/badge/Prisma-ORM-2D3748?style=for-the-badge&logo=prisma)](https://www.prisma.io/)
[![Tailwind CSS](https://img.shields.io/badge/Tailwind-CSS-38B2AC?style=for-the-badge&logo=tailwind-css)](https://tailwindcss.com/)
[![Google Gemini](https://img.shields.io/badge/AI-Google_Gemini-4285F4?style=for-the-badge)](https://deepmind.google/technologies/gemini/)

</div>

<br />

WorkWise is an enterprise-grade, full-stack application architected to orchestrate Large Language Models (LLMs), real-time peer-to-peer protocols, and event-driven background processing. Designed as a comprehensive career intelligence platform, it handles complex data synthesis to generate ATS-optimized artifacts and dynamic market analytics.

This repository serves as a demonstration of advanced system design, scalable full-stack development, and seamless integration of third-party APIs within a modern React/Next.js ecosystem.

---

## Core Platform Capabilities

- **Real-Time Interview Simulation**: Implements low-latency, role-specific mock interviews, delivering instantaneous and actionable feedback to candidates.
- **Data-Driven Market Analytics**: Aggregates and visualizes real-time industry trends, compensation medians, and skill-demand metrics.
- **Algorithmic Resume Generation**: Dynamically generates ATS-optimized, pixel-perfect resumes by leveraging advanced markdown parsing and AI assistance.
- **Context-Aware Document Synthesis**: Utilizes Generative AI to contextually fuse user profiles with target job descriptions, programmatically producing highly targeted cover letters.

---

## Technical Architecture & Core Complexity

WorkWise was engineered to solve complex state management and asynchronous processing challenges common in AI-heavy applications. The architecture relies on several advanced paradigms to maintain high availability and a seamless user experience.

### System Architecture Diagram

```mermaid
flowchart TB
    Client["Client (Next.js/React)"]
    
    subgraph "Application Server (Next.js 15)"
        ServerActions["Server Actions / API"]
        PrismaClient["Prisma Client"]
        InngestAPI["Inngest Workers"]
    end
    
    subgraph "External Providers"
        Clerk["Clerk (Authentication)"]
        Gemini["Google Gemini (LLM)"]
        InngestCloud["Inngest Cloud (Broker)"]
    end
    
    DB[("PostgreSQL Database")]
    
    Client -- "JWT / Session" --> Clerk
    Client -- "Server Actions" --> ServerActions
    Client -- "WebRTC Signaling" --> Client
    
    ServerActions -- "Event Dispatch" --> InngestCloud
    InngestCloud -- "Trigger Job Execution" --> InngestAPI
    
    ServerActions -- "Sync Queries" --> PrismaClient
    InngestAPI -- "Async Data Mutates" --> PrismaClient
    PrismaClient -- "TCP/IP" --> DB
    
    ServerActions -- "Streaming AI Prompts" --> Gemini
    InngestAPI -- "Heavy Context Generation" --> Gemini
```

### 1. LLM Orchestration & Contextual Synthesis
The platform integrates **Google Generative AI (Gemini)** to power a contextual career mentor and a dynamic cover letter generator. Rather than simple API wrappers, the system aggregates user telemetry (skills, JSON-structured assessment histories, and industry data) to construct highly specific prompt contexts, ensuring deterministic and highly relevant AI outputs.

### 2. Event-Driven Asynchronous Processing
AI payload generation and heavy data aggregation tasks are decoupled from the main thread using **Inngest**. By implementing an event-driven background processing model, the application prevents serverless function timeouts, guarantees fault-tolerant task execution, and maintains ultra-low latency for the client-side UI during resource-intensive operations.

### 3. Real-Time WebRTC Integration
The interview preparation module implements **PeerJS** to establish peer-to-peer WebRTC connections. This allows for low-latency, real-time media streams and data channel communications, bypassing server bottlenecks for live mock interview simulations.

### 4. Strict Type Safety & Relational Modeling
End-to-end type safety is enforced from the database layer to the React DOM. **Prisma ORM** manages a normalized PostgreSQL schema featuring complex relationships (1:1, 1:M) and heavily utilized JSON columns for unstructured analytics. **Zod** validates all mutations and form schemas, drastically reducing runtime exceptions.

### 5. Algorithmic ATS Artifact Generation
The Resume Builder combines `@uiw/react-md-editor` for robust markdown parsing with custom algorithms to calculate real-time Applicant Tracking System (ATS) compatibility scores. The document generation pipeline utilizes `html2pdf.js` to render the DOM dynamically into pixel-perfect PDF blobs.

---

## Technology Stack

**Frontend Infrastructure:**
- **Framework:** Next.js 15 (App Router, Turbopack for compilation speed)
- **Library:** React 19 (Leveraging modern concurrent rendering features)
- **State & UI:** Tailwind CSS v4, Framer Motion (Hardware-accelerated animations)
- **Component Primitives:** Radix UI (Unstyled, strictly accessible components)
- **Data Visualization:** Recharts (SVG-based charting for industry metrics)

**Backend Infrastructure:**
- **Runtime:** Node.js Edge / Serverless deployment model
- **Database:** PostgreSQL (Cloud-native deployment)
- **ORM:** Prisma v6.5 (Strictly typed query builder and schema migration)
- **Authentication:** Clerk Next.js (Multi-tenant, JWT-based secure sessions)
- **Job Queue:** Inngest (Serverless event-driven worker execution)

---

## Entity-Relationship (ER) Model & Database Architecture

The WorkWise database architecture is built on **PostgreSQL** managed through **Prisma ORM**. It balances relational normalization with semi-structured JSON storage to efficiently serve real-time dashboard analytics, track interview progress, and supply rich context to the Generative AI engine.

### ER Model Diagram

```mermaid
erDiagram
    IndustryInsight ||--o{ User : "categorizes"
    User ||--o{ Assessment : "takes"
    User ||--|| Resume : "owns"
    User ||--o{ CoverLetter : "generates"

    User {
        String id PK
        String clerkUserId UK "Clerk Auth ID"
        String email UK
        String name
        String imageUrl
        String industry FK "References IndustryInsight(industry)"
        String bio
        Int experience "Years of experience"
        String_Array skills
        DateTime createdAt
        DateTime updatedAt
    }

    Assessment {
        String id PK
        String userId FK "References User(id)"
        Float quizScore
        Json_Array questions "Array of quiz Q&A items"
        String category "Technical, Behavioral, etc."
        String improvementTip "AI feedback snippet"
        DateTime createdAt
        DateTime updatedAt
    }

    Resume {
        String id PK
        String userId FK,UK "References User(id) - 1:1 constraint"
        String content "Markdown text payload"
        Float atsScore
        String feedback
        DateTime createdAt
        DateTime updatedAt
    }

    CoverLetter {
        String id PK
        String userId FK "References User(id)"
        String content "Markdown text payload"
        String jobDescription
        String companyName
        String jobTitle
        String status "draft | completed"
        DateTime createdAt
        DateTime updatedAt
    }

    IndustryInsight {
        String id PK
        String industry UK "Unique key e.g. tech-software-development"
        Json_Array salaryRanges "Array of {role, min, max, median}"
        Float growthRate
        String demandLevel "High | Medium | Low"
        String_Array topSkills
        String marketOutlook "Positive | Neutral | Negative"
        String_Array keyTrends
        String_Array recommendedSkills
        DateTime lastUpdated
        DateTime nextUpdate
    }
```

### Entity Dictionary & Key Attributes

1. **`User` Entity**: Central actor in the system. Stores Clerk authentication mapping (`clerkUserId`), profile details (bio, experience level, skills array), and foreign keys linking candidates to industry market insights.
2. **`Assessment` Entity**: Immutable records of candidate interview performance. Stores overall `quizScore`, structured JSON logs of all questions & user responses (`questions`), category classification, and AI-generated `improvementTip`.
3. **`Resume` Entity**: Markdown-formatted user resume artifact for editing and scoring. Captures AI evaluation metadata including `atsScore` and structured actionable `feedback`. Strictly 1:1 linked per candidate.
4. **`CoverLetter` Entity**: Tailored cover letter artifacts targeting specific applications. Stores `jobDescription`, `companyName`, `jobTitle`, workflow `status`, and generated markdown `content`.
5. **`IndustryInsight` Entity**: Central analytics hub aggregating industry growth rates, demand levels, market outlook, key trends, and salary range breakdowns (`salaryRanges` JSON array) for domain-specific insights.

### Relationships & Cardinalities Explained

- **`IndustryInsight` to `User` (1 : N - One-to-Many, Optional)**:
  - An `IndustryInsight` record can categorize multiple `User` profiles sharing that industry slug (`User.industry` foreign key -> `IndustryInsight.industry`).
  - Optional on the `User` side to allow new user registration prior to selecting an industry sector.

- **`User` to `Assessment` (1 : N - One-to-Many)**:
  - A `User` can take multiple interview simulations over time. Each assessment belongs strictly to one user via `userId`.
  - Indexed via `@@index([userId])` for rapid history loading on candidate dashboards.

- **`User` to `Resume` (1 : 1 - One-to-One)**:
  - Enforced by a `@unique` constraint on `Resume.userId`. Each candidate maintains exactly one master resume document within the platform.

- **`User` to `CoverLetter` (1 : N - One-to-Many)**:
  - Candidates can generate and store multiple cover letters for different applications (`companyName`, `jobTitle`).
  - Indexed via `@@index([userId])` for fast candidate-specific retrieval.

### Schema Design Rationales

- **Hybrid Semi-Structured Architecture**: Incorporates PostgreSQL native `Json[]` arrays (`questions` in `Assessment` and `salaryRanges` in `IndustryInsight`). This avoids heavy relational multi-table joins while keeping complex structures easily queryable.
- **Low-Latency Indexing**: Strategic single-column indexes (`@@index([userId])` and `@@index([industry])`) optimize response times for Server Actions and API routes under high concurrency.
- **Generative AI Context Pipeline**: Historical performance data (`Assessment.improvementTip`) and industry metrics (`IndustryInsight`) are retrieved directly from the schema and injected into Google Gemini LLM prompt streams for context-aware content synthesis.

---

## Local Development Setup

### 1. Repository Initialization
```bash
git clone https://github.com/yourusername/WorkWise-Final.git
cd WorkWise-Final
npm install
```

### 2. Environment Configuration
Establish a `.env` file in the project root with the following requisite keys:
```env
# Database Credentials
DATABASE_URL="postgresql://user:password@host:port/db"

# Authentication (Clerk)
NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY=pk_test_...
CLERK_SECRET_KEY=sk_test_...
NEXT_PUBLIC_CLERK_SIGN_IN_URL=/sign-in
NEXT_PUBLIC_CLERK_SIGN_UP_URL=/sign-up

# Generative AI Integrations
GEMINI_API_KEY=your_gemini_api_key

# Event-Driven Webhooks
INGEST_SIGNING_KEY=your_inngest_key
```

### 3. Database Migration
Synchronize the Prisma schema with the PostgreSQL instance and generate the typed client.
```bash
npm run build
npx prisma db push
```

### 4. Bootstrapping the Server
```bash
npm run dev
```
The application will execute on `http://localhost:3000`.

---

<br />
<div align="center">
  <i>Engineered for scale, reliability, and unparalleled user experience.</i>
</div>
