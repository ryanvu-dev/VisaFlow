| « [Prev](./1-project-setup.md) | [🏠︎](./README.md) | Next » |
| --- | --- | --- |

---

<h1 align="center"> 🚀 DevOps Setup</h1>

![devops-setup-banner](../banners/devops-setup-banner.png)

<br/>

Below is the complete deployment setup for VisaFlow using Render (backend + DB) and Vercel (frontend).

---

## 1. Repository Preparation

| Step | Action |
|------|--------|
| **1.1** | <details><summary><strong>Prepare folder structure</strong></summary><br/><pre>/backend → .NET 8 API<br/>/frontend → Next.js</pre></details> |
| **1.2** | <details><summary><strong>Ensure .env files are ignored</strong></summary><br/><pre>.env<br/>.env.local<br/>.env.*</pre></details> |
| **1.3** | Enable automatic migrations in <code>Program.cs</code>. |

---

## 2. Local Development Setup

| Step | Action |
|------|--------|
| **2.1** | Backend .env created locally |
| **2.2** | Frontend .env.local created locally |
| **2.3** | Backend: <pre>dotnet watch run</pre> Frontend: <pre>npm run dev</pre> |

---

## 3. Render Postgres Setup (Production DB)

| Step | Action |
|------|--------|
| **3.1** | Create Render PostgreSQL instance |
| **3.2** | Copy Internal DB URL |
| **3.3** | No manual networking required |

---

## 4. Backend Deployment (Render)

| Step | Action |
|------|--------|
| **4.1** | <details><summary><strong>Create Render Web Service</strong></summary><br/>Runtime: .NET 8<br/>Root: <code>/backend</code></details> |
| **4.2** | <details><summary><strong>Build & start commands</strong></summary><br/>Build:<br/><pre>dotnet publish -c Release -o out</pre>Start:<br/><pre>dotnet out/VisaFlow.Api.dll</pre></details> |
| **4.3** | <details><summary><strong>Environment variables</strong></summary><br/><pre>ASPNETCORE_ENVIRONMENT=Production<br/>DATABASE_URL=postgres://...<br/>JWT_SECRET=your-prod-secret<br/>ALLOWED_ORIGINS=https://&lt;vercel-domain&gt;.vercel.app</pre></details> |
| **4.4** | Auto‑migrations enabled |
| **4.5** | Health endpoint at <code>/health</code> |

---

## 5. Frontend Deployment (Vercel)

| Step | Action |
|------|--------|
| **5.1** | Connect GitHub → Select <code>/frontend</code> |
| **5.2** | <details><summary><strong>Environment variables</strong></summary><br/><pre>NEXT_PUBLIC_API_URL=https://&lt;render-backend&gt;.onrender.com<br/>NEXT_PUBLIC_ENV=production</pre></details> |
| **5.3** | Build command: <code>next build</code> |
| **5.4** | Vercel auto‑creates preview deployments |

---

## 6. CI Setup (Tests Only)

| Step | Action |
|------|--------|
| **6.1** | Backend CI: restore + build + test |
| **6.2** | Frontend CI: install + lint + build |
| **6.3** | No deploy steps (Render/Vercel handle deploys) |

---

## 7. Deployment Workflow

| Step | Action |
|------|--------|
| **7.1** | <details><summary><strong>Create feature branch</strong></summary><br/><pre>git checkout -b feature/&lt;name&gt;</pre></details> |
| **7.2** | Develop locally |
| **7.3** | Push → CI runs → Vercel preview |
| **7.4** | PR → Review → Test preview |
| **7.5** | Merge to <code>main</code> → Auto‑deploy |
| **7.6** | Validate logs + <code>/health</code> |

---

## 8. Logging & Monitoring

| Step | Action |
|------|--------|
| **8.1** | Render logs (build + runtime) |
| **8.2** | Vercel logs (build + runtime) |
| **8.3** | Optional: Sentry, Seq, UptimeRobot |

---

## 9. Hotfix Workflow

| Step | Action |
|------|--------|
| **9.1** | Branch from <code>main</code> |
| **9.2** | Fix issue |
| **9.3** | Push → CI |
| **9.4** | Merge to <code>main</code> |
| **9.5** | Auto‑deploy |
| **9.6** | Validate logs |

---

## 10. What’s Intentionally Skipped (MVP Mode)

| Step | Action |
|------|--------|
| **10.1** | No staging environment |
| **10.2** | No Docker/Kubernetes |
| **10.3** | No Terraform/IaC |
| **10.4** | No GitHub deploy actions |
| **10.5** | No blue/green deploys |

---

| « [Prev](./1-project-setup.md) | [🏠︎](./README.md) | Next » |
| --- | --- | --- |
