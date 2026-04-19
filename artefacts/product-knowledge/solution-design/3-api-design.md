| « [Prev](./2-data-model.md) | [🏠︎](../README.md) | [Next](./4-system-architecture.md) » |
| --- | --- | --- |

---

# ⚙️ API Design

![solution-design-banner](../../banners/solution-design-banner.png)

<br/>

This page defines the API surface and core domain operations for VisaFlow. It describes the main resources, endpoint patterns, request/response shapes, and behavioral constraints needed to implement the workflow, application, document, and coordinator/applicant interaction flows.

The API is intentionally simple, predictable, and aligned with the linear workflow philosophy of the MVP.

---

## 1. Goals

* Expose workflows and application instances cleanly  
* Keep the API lightweight and easy to iterate for a personal project  
* Support embedded step data while preserving separate document/comment references  
* Allow coordinators and applicants to operate through distinct but related endpoints  
* Enable future integration with external authority updates and notifications  
* Enforce workflow state transitions and coordinator–applicant interaction loops  

---

## 2. Core Resources

* `User`  
* `Workflow`  
* `Application`  
* `Document`  
* `Comment`  
* `ExternalStatusUpdate`  

These map directly to the data model and workflow architecture.

---

## 3. Authentication & Authorisation

* Use simple token-based auth (JWT, magic-link token, or session token).  
* Roles: `APPLICANT`, `COORDINATOR`.  
* Applicants can access only their own applications and related documents/comments.  
* Coordinators can access workflows they own and applications created from those workflows.  

### Endpoints

| Method | Endpoint | Description |
| --- | --- | --- |
| POST | `/auth/request-link` | Request a magic link |
| POST | `/auth/verify` | Verify token and return session |

---

## 4. Resource Endpoints

Below is the unified endpoint set combining your version and the expanded version.

### 4.1 Workflows

`GET /:coordinator_id/workflows`  
* List workflows owned by a coordinator.

`GET /workflows/:workflow_id`  
* Fetch a workflow and its embedded step definitions.

`POST /workflows`  
* Create a new workflow (coordinator only).

`PUT /workflows/:workflow_id`  
* Update workflow metadata or steps.

`DELETE /workflows/:workflow_id`  
* Archive or delete a workflow.

### 4.2 Applications

`GET /applications`  
* List applications for the current user (role‑aware).

`GET /applications/:application_id`  
* Fetch an application and its embedded step instances.

`POST /applications`  
* Create a new application instance from a workflow.  
* Body includes: `workflow_id`, `applicant_id`, optional metadata.

`PUT /applications/:application_id`  
* Update application-level fields such as `title`, `status`, or step answers.

`PATCH /applications/:application_id/status`  
* Change overall application state (e.g., `IN_PROGRESS → SUBMITTED_TO_COORDINATOR`).

### 4.3 Steps (Application Steps)

These endpoints support the applicant–coordinator interaction loop.

`GET /applications/:application_id/steps`  
* List all step instances.

`GET /applications/:application_id/steps/:step_id`  
* Fetch a single step instance.

`POST /applications/:application_id/steps/:step_id/submit`  
* Applicant submits a step.

`POST /applications/:application_id/steps/:step_id/request-correction`  
* Coordinator requests changes.

`POST /applications/:application_id/steps/:step_id/approve`  
* Coordinator approves the step.

### 4.4 Documents

`POST /applications/:application_id/documents`  
* Upload a document.  
* Body includes `step_instance_id` + file.

`GET /applications/:application_id/documents`  
* List documents for the application.

`GET /documents/:document_id`  
* Fetch document metadata.

`PUT /documents/:document_id`  
* Replace or update document metadata.

### 4.5 Comments

`POST /applications/:application_id/comments`  
* Add a comment to a step or document.  
* Body may include `step_instance_id` and optional `document_id`.

`GET /applications/:application_id/comments`  
* List comments for the application.

### 4.6 External Status Updates

`POST /applications/:application_id/external-status-updates`  
* Coordinator records a new external authority update.

`GET /applications/:application_id/external-status-updates`  
* List external status updates.

---

## 5. Data Shape Examples

### 5.1 Workflow Response

```json
{
  "id": "workflow-123",
  "name": "Visitor Visa",
  "version": "1.0",
  "coordinator_id": "user-456",
  "metadata": { "country": "Australia" },
  "steps": [
    {
      "step_id": "personal-info",
      "title": "Personal Information",
      "instructions": "Enter your personal details.",
      "inputs_config": [
        { "component_id": "first_name", "type": "text", "label": "First name", "required": true }
      ],
      "order": 1,
      "is_required": true
    }
  ]
}
```

### 5.2 Application Response

```json
{
  "id": "app-789",
  "workflow_id": "workflow-123",
  "applicant_id": "user-111",
  "steps": [
    {
      "instance_id": "appstep-1",
      "workflow_step_id": "personal-info",
      "step_answers": { "first_name": "Aunty" },
      "document_ids": ["doc-1"],
      "comment_ids": ["comment-1"],
      "status": "SUBMITTED",
      "due_date": "2026-05-01T00:00:00Z"
    }
  ],
  "status": "IN_PROGRESS",
  "title": "Aunty's Visitor Visa"
}
```

---

## 6. API Behaviour Notes

* `steps` remain embedded in workflow/application payloads.  
* `Document` and `Comment` are stored separately and referenced by ID.  
* Workflow ownership is defined by `Workflow.coordinator_id`.  
* Application ownership is defined by `Application.applicant_id`.  
* Coordinators can update application status and external status updates.  
* State machine rules must be enforced (e.g., cannot approve a non‑submitted step).  

---

## 7. Error Handling

| Code | Meaning |
| --- | --- |
| 400 | Invalid input |
| 401 | Invalid or expired token |
| 403 | Role not permitted |
| 404 | Resource not found |
| 409 | Invalid state transition |

<u>Examples:</u>  
* Cannot approve a step that is not `SUBMITTED`.  
* Cannot submit a step missing required fields.  

---

## 8. Summary

This API design provides a clean, minimal, workflow‑driven interface that supports the entire MVP:

* Applicants complete steps  
* Coordinators review and approve  
* Documents and comments flow naturally  
* External status is tracked  
* State transitions remain predictable and enforced  

This document bridges the gap between the conceptual architecture and implementation.

---

| « [Prev](./2-data-model.md) | [🏠︎](../README.md) | [Next](./4-system-architecture.md) » |
| --- | --- | --- |
