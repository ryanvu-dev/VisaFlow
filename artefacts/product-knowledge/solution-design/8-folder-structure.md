| « [Prev](./7-database-schema.md) | [🏠︎](../README.md) | Next » |
| --- | --- | --- |

---

# 📁 Folder Structure

![solution-design-banner](../../banners/solution-design-banner.png)

<br/>

This document defines the recommended **folder structure** for the VisaFlow project.  
It is designed for:

- Clean, maintainable architecture  
- Strong separation of concerns  
- Alignment with **Onion Architecture**  
- Easy deployment on **Render**  
- A **Next.js frontend** and **.NET backend**  
- A workflow‑driven domain model  

The structure below reflects your preferences for clarity, modularity, and strongly typed boundaries.

---

## 1. High-Level Overview

VisaFlow consists of two main applications:

- **frontend/** → Next.js 14 (App Router, React Query, client‑side rendering)  
- **backend/** → .NET 8 Web API (Clean Architecture, EF Core, Postgres)  

Shared assets (docs, diagrams, scripts) live at the root.

```
visaflow/
├── backend/
├── frontend/
├── docs/
├── scripts/
└── README.md
```

---

## 2. Backend Folder Structure (`/backend`)

The backend follows **Onion Architecture**:

- **Domain** → pure business logic  
- **Application** → use cases  
- **Infrastructure** → EF Core, Postgres, file storage  
- **API** → controllers, DTOs  

```
backend/
├── src/
│   ├── VisaFlow.Api/                 # Presentation layer (controllers, endpoints)
│   ├── VisaFlow.Application/         # Use cases, DTOs, validators
│   ├── VisaFlow.Domain/              # Entities, value objects, enums, interfaces
│   ├── VisaFlow.Infrastructure/      # EF Core, repositories, migrations, services
│   └── VisaFlow.Shared/              # Shared abstractions (optional)
│
├── tests/
│   ├── VisaFlow.Domain.Tests/
│   ├── VisaFlow.Application.Tests/
│   └── VisaFlow.Api.Tests/
│
└── README.md
```

### 2.1 Domain Layer

```
VisaFlow.Domain/
├── Entities/
│   ├── User.cs
│   ├── Workflow.cs
│   ├── Application.cs
│   ├── Document.cs
│   ├── Comment.cs
│   └── ExternalStatusUpdate.cs
│
├── ValueObjects/
│   ├── WorkflowStep.cs
│   └── ApplicationStep.cs
│
├── Enums/
│   ├── UserRole.cs
│   ├── ApplicationStatus.cs
│   └── DocumentUploadStatus.cs
│
└── Interfaces/
├── IRepository.cs
├── IWorkflowRepository.cs
├── IApplicationRepository.cs
└── IFileStorageService.cs
```

### 2.2 Application Layer

```
VisaFlow.Application/
├── UseCases/
│   ├── Applications/
│   ├── Workflows/
│   ├── Documents/
│   └── Comments/
│
├── DTOs/
├── Validators/
└── Common/
```

### 2.3 Infrastructure Layer

```
VisaFlow.Infrastructure/
├── Persistence/
│   ├── VisaFlowDbContext.cs
│   ├── Configurations/
│   └── Migrations/
│
├── Repositories/
├── FileStorage/
└── Services/
```


### 2.4 API Layer

```
VisaFlow.Api/
├── Controllers/
│   ├── ApplicationsController.cs
│   ├── WorkflowsController.cs
│   ├── DocumentsController.cs
│   └── StatusUpdatesController.cs
│
├── Endpoints/ (if using Minimal APIs)
├── Filters/
├── Middleware/
└── Program.cs
```

---

## 3. Frontend Folder Structure (`/frontend`)

The frontend uses **Next.js 14 App Router**, **React Query**, and **client‑side rendering** for most pages.

```
frontend/
├── app/
│   ├── layout.tsx
│   ├── page.tsx
│   ├── applications/
│   ├── workflows/
│   ├── steps/
│   └── status/
│
├── components/
│   ├── ui/
│   ├── forms/
│   ├── steps/
│   └── layout/
│
├── hooks/
│   ├── useApplication.ts
│   ├── useWorkflow.ts
│   └── useDocumentUpload.ts
│
├── lib/
│   ├── api/
│   ├── validators/
│   └── utils/
│
├── styles/
├── public/
└── README.md
```


### 3.1 App Router Structure

```
app/
├── applications/
│   ├── [id]/
│   │   ├── page.tsx
│   │   ├── steps/
│   │   └── status/
│   └── new/
│       └── page.tsx
│
├── workflows/
│   ├── [id]/
│   └── new/
│
└── page.tsx
```


### 3.2 Components

```
components/
├── ui/                # Buttons, inputs, cards
├── forms/             # Form builders, field components
├── steps/             # Step viewer, step editor
└── layout/            # Navigation, headers, wrappers
```

---

## 4. Shared Project Assets

```
docs/
├── 1-product-discovery.md
├── 2-user-journey-map.md
├── 3-problem-scenarios-use-cases.md
├── 4-mvp-storyboard.md
├── 5-implementation-plan.md
├── 6-data-model.md
├── 7-database-schema.md
└── 8-folder-structure.md

scripts/
├── seed-data.sql
└── local-dev.sh
```

---

## 5. Summary

This folder structure:

- Aligns with **Onion Architecture**  
- Supports a clean separation between frontend and backend  
- Keeps domain logic pure and testable  
- Matches your workflow engine design  
- Works perfectly for a personal project deployed on Render  
- Scales cleanly as VisaFlow grows  

---

| « [Prev](./7-database-schema.md) | [🏠︎](../README.md) | Next » |
| --- | --- | --- |
