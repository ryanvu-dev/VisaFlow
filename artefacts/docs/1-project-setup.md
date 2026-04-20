| « Prev | 🏠︎ | [Next](./2-devops-setup.md) » |
| --- | --- | --- |

---

<h1 align="center"> 🛠️ Project Setup</h1>

![project-setup-banner](../banners/project-setup-banner.png)

<br/>

This guide describes how to set up VisaFlow for **local development** on your machine.

---

## 1. Prerequisites

| Step | Action |
|------|--------|
| **1.1** | Install **.NET 8 SDK** |
| **1.2** | Install **Node.js 20+** |
| **1.3** | Install **PostgreSQL 15+** |
| **1.4** | Install **EF Core Tools**:<br/><pre>dotnet tool install --global dotnet-ef</pre> |

---

## 2. Backend Setup (.NET 8)

| Step | Action |
|------|--------|
| **2.1** | <details><summary><strong>Create backend .env</strong></summary><br/><pre>ASPNETCORE_ENVIRONMENT=Development<br/>CONNECTION_STRING=Host=localhost;Port=5432;Database=visaflow;Username=postgres;Password=postgres<br/>JWT_SECRET=local-dev-secret</pre></details> |
| **2.2** | <details><summary><strong>Restore dependencies</strong></summary><br/><pre>cd backend<br/>dotnet restore</pre></details> |
| **2.3** | <details><summary><strong>Apply migrations</strong></summary><br/><pre>dotnet ef database update</pre></details> |
| **2.4** | <details><summary><strong>Run backend</strong></summary><br/><pre>dotnet watch run</pre></details> |

Backend runs at:  
**http://localhost:5000**

---

## 3. Frontend Setup (Next.js)

| Step | Action |
|------|--------|
| **3.1** | <details><summary><strong>Create frontend .env.local</strong></summary><br/><pre>NEXT_PUBLIC_API_URL=http://localhost:5000<br/>NEXT_PUBLIC_ENV=development</pre></details> |
| **3.2** | <details><summary><strong>Install dependencies</strong></summary><br/><pre>cd frontend<br/>npm install</pre></details> |
| **3.3** | <details><summary><strong>Run frontend</strong></summary><br/><pre>npm run dev</pre></details> |

Frontend runs at:  
**http://localhost:3000**

---

## 4. Database (Local Postgres)

| Step | Action |
|------|--------|
| **4.1** | Create database:<br/><pre>createdb visaflow</pre> |
| **4.2** | Ensure Postgres is running on port **5432** |
| **4.3** | Migrations apply automatically on backend startup (if enabled) |

---

## 5. Optional Tools

| Tool | Purpose |
|------|---------|
| **pgAdmin / TablePlus** | GUI for Postgres |
| **Thunder Client / Postman** | API testing |
| **EF Core Power Tools** | Visualise DB schema |

---

## 6. Troubleshooting

| Issue | Fix |
|-------|-----|
| Backend cannot connect to DB | Check `CONNECTION_STRING` |
| Migrations fail | Ensure DB exists |
| CORS errors | Update `ALLOWED_ORIGINS` in backend |
| Frontend cannot reach backend | Check `NEXT_PUBLIC_API_URL` |

---

| « Prev | 🏠︎ | [Next](./2-devops-setup.md) » |
| --- | --- | --- |
