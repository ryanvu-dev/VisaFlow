| 📖 | [Product Knowledge](./product-knowledge/README.md) |
| --- | --- |

---

<h1 align="center"> 🚀 DevOps Setup</h1>

![devops-guidelines-banner](./banners/devops-guidelines-banner.png)

<br/>

Below is the complete deployment setup for VisaFlow using Render (backend + DB) and Vercel (frontend).

---

## 1. Repository Preparation

| Step | Action |
|------|--------|
| **1.1** | <details><summary><strong>Prepare folder structure</strong></summary><br/>Ensure the repo contains:<br/><pre>/backend → .NET 8 Minimal API<br/>/frontend → Next.js (App Router)</pre><br/>Backend must support <code>dotnet publish</code> and frontend must support <code>next build</code>.<br/></details> |
| **1.2** | <details><summary><strong>Ensure .env files are ignored</strong></summary><br/>Add to <code>.gitignore</code>:<br/><pre>.env<br/>.env.local<br/>.env.*</pre></details> |
| **1.3** | Enable automatic migrations in <code>Program.cs</code>. |

---

## 2. Local Development Setup

| Step | Action |
|------|--------|
| **2.1** | <details><summary><strong>Create backend .env</strong></summary><br/><pre>ASPNETCORE_ENVIRONMENT=Development<br/>DATABASE_URL=Host=localhost;Port=5432;Database=visaflow;Username=...<br/>JWT_SECRET=local-dev-secret</pre></details> |
| **2.2** | <details><summary><strong>Create frontend .env.local</strong></summary><br/><pre>NEXT_PUBLIC_API_URL=http://localhost:5000<br/>NEXT_PUBLIC_ENV=development</pre></details> |
| **2.3** | <details><summary><strong>Run local services</strong></summary><br/>Backend:<br/><pre>dotnet ef database update<br/>dotnet watch run</pre><br/>Frontend:<br/><pre>npm install<br/>npm run dev</pre></details> |

---

## 3. Render Postgres Setup (Production DB)

| Step | Action |
|------|--------|
| **3.1** | Create a new Render PostgreSQL instance (Free Tier). |
| **3.2** | Copy the Internal Database URL for backend environment variables. |
| **3.3** | Render auto‑provisions storage and networking. |

---

## 4. Backend Deployment (Render)

| Step | Action |
|------|--------|
| **4.1** | <details><summary><strong>Create Render Web Service</strong></summary><br/>Render → New → Web Service → Connect GitHub → Select <code>/backend</code><br/>Runtime: <code>.NET 8</code><br/></details> |
| **4.2** | <details><summary><strong>Set build & start commands</strong></summary><br/>Build:<br/><pre>dotnet publish -c Release -o out</pre>Start:<br/><pre>dotnet out/VisaFlow.Api.dll</pre></details> |
| **4.3** | <details><summary><strong>Add environment variables</strong></summary><br/><pre>ASPNETCORE_ENVIRONMENT=Production<br/>DATABASE_URL=postgres://...<br/>JWT_SECRET=your-prod-secret<br/>ALLOWED_ORIGINS=https://&lt;your-vercel-domain&gt;.vercel.app</pre></details> |
| **4.4** | Enable automatic migrations in <code>Program.cs</code>. |
| **4.5** | Expose a health endpoint at <code>/health</code>. |

---

## 5. Frontend Deployment (Vercel)

| Step | Action |
|------|--------|
| **5.1** | Connect GitHub repo → Select <code>/frontend</code> → Framework: Next.js. |
| **5.2** | <details><summary><strong>Add environment variables</strong></summary><br/><pre>NEXT_PUBLIC_API_URL=https://&lt;your-render-backend&gt;.onrender.com<br/>NEXT_PUBLIC_ENV=production</pre></details> |
| **5.3** | Build command: <code>next build</code> • Output: <code>.next</code> |
| **5.4** | Vercel automatically creates preview deployments for PRs. |

---

## 6. CI Setup (Tests Only)

| Step | Action |
|------|--------|
| **6.1** | Backend CI runs restore + tests. |
| **6.2** | Frontend CI runs install + tests. |
| **6.3** | No build stage needed (Render + Vercel handle builds). |

---

## 7. Deployment Workflow

| Step | Action |
|------|--------|
| **7.1** | <details><summary><strong>Create feature branch</strong></summary><br/><pre>git checkout -b feature/&lt;name&gt;</pre></details> |
| **7.2** | Develop locally using <code>dotnet watch run</code> and <code>npm run dev</code>. |
| **7.3** | Push branch → CI runs tests → Vercel creates preview deployment. |
| **7.4** | Open PR → Review code → Test preview URL. |
| **7.5** | Merge to <code>main</code> → Render + Vercel auto‑deploy. |
| **7.6** | Verify deployment using logs + <code>/health</code>. |

---

## 8. Logging & Monitoring

| Step | Action |
|------|--------|
| **8.1** | Render logs: console, build, runtime, per‑deploy. |
| **8.2** | Vercel logs: build, runtime, edge. |
| **8.3** | Optional later: Sentry, Seq, UptimeRobot. |

---

## 9. Hotfix Workflow

| Step | Action |
|------|--------|
| **9.1** | Branch from <code>main</code>. |
| **9.2** | Fix issue. |
| **9.3** | Push → CI runs tests. |
| **9.4** | Merge to <code>main</code>. |
| **9.5** | Auto‑deploy. |
| **9.6** | Validate logs + <code>/health</code>. |

---

## 10. What Intentionally Skipped (MVP Mode)

| Step | Action |
|------|--------|
| **10.1** | No staging environment. |
| **10.2** | No Docker or Kubernetes. |
| **10.3** | No Terraform or IaC. |
| **10.4** | No GitHub Actions deploy steps. |
| **10.5** | No blue/green deploys. |

---

| 📖 | [Product Knowledge](./product-knowledge/README.md) |
| --- | --- |
