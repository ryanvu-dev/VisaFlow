| « Prev | [🏠︎](./README.md) | [Next](./2-devops-setup.md) » |
| --- | --- | --- |

---

<h1 align="center"> 🛠️ Project Setup</h1>

![project-setup-banner](../banners/project-setup-banner.png)

<br/>

This guide describes how to set up VisaFlow for **local development** on your machine.

---

## 1. Prerequisites

| Step | Tool | Action |
|------|------|--------|
| **1.1** | **.NET 8 SDK** | Download from [dotnet.microsoft.com/download/dotnet/8.0](https://dotnet.microsoft.com/download/dotnet/8.0) — install the **SDK**, not just the runtime |
| **1.2** | **Node.js 20+** | Download from [nodejs.org](https://nodejs.org) |
| **1.3** | **PostgreSQL 15+** | Download from [postgresql.org](https://www.postgresql.org/download) |
| **1.4** | **EF Core Tools** | `dotnet tool install --global dotnet-ef` |
| **1.5** | **Seq** | Run locally via Docker: `docker run -e ACCEPT_EULA=Y -p 5341:5341 -p 8080:80 datalust/seq` |

Verify your installs:

```bash
dotnet --list-sdks     # should show 8.x.x
node --version         # should show 20+
psql --version         # should show 15+
dotnet ef --version    # should show latest
```

---

## 2. Backend Setup (.NET 8)

### 2.1 Create the solution and projects

```bash
dotnet new sln -n VisaFlow
```

Create the four projects:

```bash
dotnet new classlib -n VisaFlow.Domain -o src/VisaFlow.Domain
dotnet new classlib -n VisaFlow.Application -o src/VisaFlow.Application
dotnet new classlib -n VisaFlow.Infrastructure -o src/VisaFlow.Infrastructure
dotnet new webapi -n VisaFlow.Api -o src/VisaFlow.Api --use-minimal-apis
```

Add projects to the solution:

```bash
dotnet sln add src/VisaFlow.Domain/VisaFlow.Domain.csproj
dotnet sln add src/VisaFlow.Application/VisaFlow.Application.csproj
dotnet sln add src/VisaFlow.Infrastructure/VisaFlow.Infrastructure.csproj
dotnet sln add src/VisaFlow.Api/VisaFlow.Api.csproj
```

### 2.2 Wire up project references

Dependencies always point **inward** toward the domain:

```bash
# Application depends on Domain
dotnet add src/VisaFlow.Application/VisaFlow.Application.csproj \
    reference src/VisaFlow.Domain/VisaFlow.Domain.csproj

# Infrastructure depends on Domain
dotnet add src/VisaFlow.Infrastructure/VisaFlow.Infrastructure.csproj \
    reference src/VisaFlow.Domain/VisaFlow.Domain.csproj

# API depends on Application and Infrastructure
dotnet add src/VisaFlow.Api/VisaFlow.Api.csproj \
    reference src/VisaFlow.Application/VisaFlow.Application.csproj
dotnet add src/VisaFlow.Api/VisaFlow.Api.csproj \
    reference src/VisaFlow.Infrastructure/VisaFlow.Infrastructure.csproj
```

### 2.3 Install NuGet packages

```bash
# Infrastructure — EF Core + Postgres
dotnet add src/VisaFlow.Infrastructure/VisaFlow.Infrastructure.csproj \
    package Microsoft.EntityFrameworkCore
dotnet add src/VisaFlow.Infrastructure/VisaFlow.Infrastructure.csproj \
    package Npgsql.EntityFrameworkCore.PostgreSQL
dotnet add src/VisaFlow.Infrastructure/VisaFlow.Infrastructure.csproj \
    package Microsoft.EntityFrameworkCore.Design

# Application — FluentValidation
dotnet add src/VisaFlow.Application/VisaFlow.Application.csproj \
    package FluentValidation

# API — FluentValidation ASP.NET integration
dotnet add src/VisaFlow.Api/VisaFlow.Api.csproj \
    package FluentValidation.AspNetCore

# API — Serilog
dotnet add src/VisaFlow.Api/VisaFlow.Api.csproj \
    package Serilog.AspNetCore
dotnet add src/VisaFlow.Api/VisaFlow.Api.csproj \
    package Serilog.Sinks.Console
dotnet add src/VisaFlow.Api/VisaFlow.Api.csproj \
    package Serilog.Sinks.Seq
dotnet add src/VisaFlow.Api/VisaFlow.Api.csproj \
    package Serilog.Sinks.BetterStack
```

### 2.4 Create test projects

```bash
dotnet new xunit -n VisaFlow.Domain.Tests -o tests/VisaFlow.Domain.Tests
dotnet new xunit -n VisaFlow.Application.Tests -o tests/VisaFlow.Application.Tests
dotnet new xunit -n VisaFlow.Api.Tests -o tests/VisaFlow.Api.Tests

dotnet sln add tests/VisaFlow.Domain.Tests/VisaFlow.Domain.Tests.csproj
dotnet sln add tests/VisaFlow.Application.Tests/VisaFlow.Application.Tests.csproj
dotnet sln add tests/VisaFlow.Api.Tests/VisaFlow.Api.Tests.csproj

# FluentAssertions for all test projects
dotnet add tests/VisaFlow.Domain.Tests/VisaFlow.Domain.Tests.csproj \
    package FluentAssertions
dotnet add tests/VisaFlow.Application.Tests/VisaFlow.Application.Tests.csproj \
    package FluentAssertions
dotnet add tests/VisaFlow.Api.Tests/VisaFlow.Api.Tests.csproj \
    package FluentAssertions

# Wire test projects to source
dotnet add tests/VisaFlow.Domain.Tests/VisaFlow.Domain.Tests.csproj \
    reference src/VisaFlow.Domain/VisaFlow.Domain.csproj
dotnet add tests/VisaFlow.Application.Tests/VisaFlow.Application.Tests.csproj \
    reference src/VisaFlow.Application/VisaFlow.Application.csproj
dotnet add tests/VisaFlow.Api.Tests/VisaFlow.Api.Tests.csproj \
    reference src/VisaFlow.Api/VisaFlow.Api.csproj
```

### 2.5 Scaffold folder structure

```bash
# Remove boilerplate
rm src/VisaFlow.Domain/Class1.cs
rm src/VisaFlow.Application/Class1.cs
rm src/VisaFlow.Infrastructure/Class1.cs

# Domain
mkdir -p src/VisaFlow.Domain/{Entities,ValueObjects,Enums,Interfaces,Exceptions}

# Application
mkdir -p src/VisaFlow.Application/Features/{Applications,Workflows,Documents,Comments}
mkdir -p src/VisaFlow.Application/Common

# Infrastructure
mkdir -p src/VisaFlow.Infrastructure/Persistence/Configurations
mkdir -p src/VisaFlow.Infrastructure/Persistence/Migrations
mkdir -p src/VisaFlow.Infrastructure/{Repositories,FileStorage,Services}

# API
mkdir -p src/VisaFlow.Api/{Endpoints,Filters,Middleware}

# Tests
mkdir -p tests/VisaFlow.Domain.Tests/{Entities,ValueObjects}
mkdir -p tests/VisaFlow.Application.Tests/Features
mkdir -p tests/VisaFlow.Api.Tests/Endpoints
```

### 2.6 Create backend environment file

Create `src/VisaFlow.Api/appsettings.Development.json`:

```json
{
  "ConnectionStrings": {
    "Default": "Host=localhost;Port=5432;Database=visaflow;Username=postgres;Password=postgres"
  },
  "Jwt": {
    "Secret": "local-dev-secret"
  },
  "Seq": {
    "ServerUrl": "http://localhost:5341"
  }
}
```

### 2.7 Configure Serilog in Program.cs

Replace the contents of `src/VisaFlow.Api/Program.cs`:

```csharp
using Serilog;

var builder = WebApplication.CreateBuilder(args);

builder.Host.UseSerilog((context, config) =>
{
    config.WriteTo.Console();

    if (context.HostingEnvironment.IsDevelopment())
        config.WriteTo.Seq("http://localhost:5341");

    if (context.HostingEnvironment.IsProduction())
        config.WriteTo.BetterStack(
            context.Configuration["Logtail:SourceToken"]);
});

var app = builder.Build();

app.MapGet("/", () => "VisaFlow API is running");

app.Run();
```

### 2.8 Apply migrations

```bash
dotnet ef database update --project src/VisaFlow.Infrastructure \
    --startup-project src/VisaFlow.Api
```

### 2.9 Run the backend

```bash
dotnet watch run --project src/VisaFlow.Api
```

Backend runs at **http://localhost:5000**

---

## 3. Frontend Setup (Next.js)

### 3.1 Create frontend environment file

Create `frontend/.env.local`:

```bash
NEXT_PUBLIC_API_URL=http://localhost:5000
NEXT_PUBLIC_ENV=development
```

### 3.2 Install dependencies

```bash
cd frontend
npm install
```

### 3.3 Run the frontend

```bash
npm run dev
```

Frontend runs at **http://localhost:3000**

---

## 4. Database (Local Postgres)

| Step | Action |
|------|--------|
| **4.1** | Create the database: `createdb visaflow` |
| **4.2** | Ensure Postgres is running on port **5432** |
| **4.3** | Run migrations via Step 2.8 above |

---

## 5. Logging (Local Seq)

Seq provides a local log UI for development.

### 5.1 Start Seq via Docker

```bash
docker run -e ACCEPT_EULA=Y -p 5341:5341 -p 8080:80 datalust/seq
```

### 5.2 Open Seq UI

Navigate to **http://localhost:8080** in your browser.

Logs from the running backend will appear here automatically. You can filter by field:

#### filter by level
@Level = 'Error'
#### filter by applicant
ApplicantId = 'abc123'
#### combine
@Level = 'Error' and ApplicantId = 'abc123'

---

## 6. Optional Tools

| Tool | Purpose |
|------|---------|
| **pgAdmin / TablePlus** | GUI for Postgres |
| **Thunder Client / Postman** | API testing |
| **EF Core Power Tools** | Visualise DB schema |
| **Docker Desktop** | Required to run Seq locally |

---

## 7. Troubleshooting

| Issue | Fix |
|-------|-----|
| Backend cannot connect to DB | Check `ConnectionStrings:Default` in `appsettings.Development.json` |
| Migrations fail | Ensure the database exists — run `createdb visaflow` |
| CORS errors | Update `ALLOWED_ORIGINS` in backend |
| Frontend cannot reach backend | Check `NEXT_PUBLIC_API_URL` in `.env.local` |
| Seq not receiving logs | Ensure Docker is running and Seq is on port 5341 |
| `dotnet ef` not found | Run `dotnet tool install --global dotnet-ef` |

---

| « Prev | [🏠︎](./README.md) | [Next](./2-devops-setup.md) » |
| --- | --- | --- |