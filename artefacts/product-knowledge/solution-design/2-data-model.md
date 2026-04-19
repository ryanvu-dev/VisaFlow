| « [Prev](./1-workflow-architecture.md) | [🏠︎](../README.md) | [Next](./3-api-design.md) » |
| --- | --- | --- |

---

# 🗂️ Data Model

![solution-design-banner](../../banners/solution-design-banner.png)

<br/>

## 1. Purpose

Based on the `problem-discovery` documents and [workflow architecture](./1-workflow-architecture.md) design, here's a proposed data model for your `VisaFlow` personal project, designed to be practical and aligned with common standards while remaining lightweight:

### 1. Core Entities

1.  User
2.  Workflow
3.  Application
4.  WorkflowStep (for reusable step definitions)
5.  ApplicationStep (instance of a step for a specific application)
6.  Document
7.  Comment
8.  ExternalStatusUpdate

---

### 2. Data Model Details

#### 2.1. User
Represents both applicants and coordinators. Roles will differentiate their permissions and views.

| Attribute | Type    | Description                                             | Notes                            |
| :-------- | :------ | :------------------------------------------------------ | :------------------------------- |
| `id`      | UUID/INT | Unique identifier for the user                         | Primary Key                      |
| `name`    | String  | Full name of the user                                   |                                  |
| `email`   | String  | Email address (for magic link/access code, communication) | Unique, Not Null                 |
| `role`    | Enum    | `APPLICANT`, `COORDINATOR`                            |                                  |
| `created_at` | DateTime | Timestamp of user creation                             | Auto-generated                   |
| `updated_at` | DateTime | Last update timestamp                                   | Auto-generated                   |

#### 2.2 Application
Represents a single visa application process owned by an applicant and instantiated from a workflow.

| Attribute       | Type       | Description                                                 | Notes                          |
| :-------------- | :--------- | :---------------------------------------------------------- | :----------------------------- |
| `id`            | UUID/INT   | Unique identifier for the application                       | Primary Key                    |
| `workflow_id`   | UUID/INT   | Foreign Key to `Workflow`                                   |                                |
| `applicant_id`  | UUID/INT   | Foreign Key to `User` (role = APPLICANT)                    |                                |
| `steps`         | JSON       | Array of embedded `ApplicationStep` objects                  | Stores step responses and related document/comment IDs |
| `visa_type`     | String     | E.g., "Visitor Visa (Subclass 600)"                       |                                |
| `target_country`| String     | E.g., "Australia"                                         |                                |
| `status`        | Enum       | `NOT_STARTED`, `IN_PROGRESS`, `SUBMITTED_TO_COORDINATOR`, `SUBMITTED_TO_IMMIGRATION`, `NEEDS_CORRECTION`, `APPROVED`, `REJECTED`, `GRANTED` | Overall application status   |
| `title`         | String     | A user-friendly title for the application (e.g., "Aunty's Visitor Visa") |                                |
| `created_at`    | DateTime   | Timestamp of application creation                           | Auto-generated                 |
| `updated_at`    | DateTime   | Last update timestamp                                       | Auto-generated                 |

#### 2.3. Workflow
Defines a workflow form used to create applications.

| Attribute    | Type       | Description                                                 | Notes                          |
| :----------- | :--------- | :---------------------------------------------------------- | :----------------------------- |
| `id`         | UUID/INT   | Unique identifier for the workflow                  | Primary Key                    |
| `name`       | String     | Name of the workflow (e.g., "Universal Skeleton", "Subclass 600") |                             |
| `version`    | String     | Version identifier for the workflow                          |                                |
| `coordinator_id` | UUID/INT | Foreign Key to `User` (role = COORDINATOR)                   | Owner/creator of the workflow |
| `description`| Text       | Optional description of the workflow                          |                                |
| `metadata`   | JSON       | Optional metadata such as language, region, or visa category |                                |
| `steps`      | JSON       | Array of embedded `WorkflowStep` objects                      | Stores step definitions and input configuration |
| `created_at` | DateTime   | Timestamp of workflow creation                               | Auto-generated                 |
| `updated_at` | DateTime   | Last update timestamp                                       | Auto-generated                 |

#### 2.4. WorkflowStep
Defines the structure and requirements for a step within a workflow. Stored as an embedded JSON object inside `Workflow.steps`.

| Attribute      | Type       | Description                                                 | Notes                                    |
| :------------- | :--------- | :---------------------------------------------------------- | :--------------------------------------- |
| `step_id`      | String     | Local identifier for the workflow step definition           | Used to reference step instances        |
| `title`        | String     | E.g., "Upload Passport Photo"                             |                                          |
| `instructions` | Text       | Detailed instructions for the step                          |                                          |
| `inputs_config`| JSON       | JSON schema for input components, validation, and catalogue references | Stores the UI input definitions for the step |
| `order`        | INT        | Display order within the workflow                    |                                          |
| `is_required`| Boolean    | Whether this step is mandatory                              | Default: `true`                          |
| `created_at` | DateTime   | Timestamp of creation                                        | Auto-generated                           |
| `updated_at` | DateTime   | Last update timestamp                                        | Auto-generated                           |

#### 2.5. ApplicationStep
An instance of a `WorkflowStep` within a specific `Application`, tracking its runtime status and user responses. Stored as an embedded JSON object inside `Application.steps`.

| Attribute        | Type       | Description                                                 | Notes                          |
| :-------------- | :--------- | :---------------------------------------------------------- | :----------------------------- |
| `instance_id`    | String     | Local identifier for the application step instance          | Used to reference step data   |
| `workflow_step_id`| String    | Identifier of the corresponding workflow step definition    | Matches `WorkflowStep.step_id`|
| `step_answers`   | JSON       | User-submitted answers or data for this step                |                                |
| `document_ids`   | JSON       | List of document IDs associated with this step              |                                |
| `comment_ids`    | JSON       | List of comment IDs associated with this step               |                                |
| `status`        | Enum       | `NOT_STARTED`, `IN_PROGRESS`, `SUBMITTED`, `NEEDS_CORRECTION`, `APPROVED` | Status for this specific step  |
| `due_date`      | DateTime   | Optional due date for completion                            |                                |
| `completed_at`  | DateTime   | Timestamp when step was approved                            |                                |
| `created_at`    | DateTime   | Timestamp of instance creation                              | Auto-generated                 |
| `updated_at`    | DateTime   | Last update timestamp                                       | Auto-generated                 |

#### 2.6. Document
Represents a file uploaded for a specific `ApplicationStep`.

| Attribute         | Type       | Description                                                 | Notes                          |
| :---------------- | :--------- | :---------------------------------------------------------- | :----------------------------- |
| `id`              | UUID/INT   | Unique identifier for the document                          | Primary Key                    |
| `step_instance_id`| String     | Identifier of the related embedded application step        | Matches `ApplicationStep.instance_id` |
| `file_name`       | String     | Original file name                                          |                                |
| `storage_path`    | String     | Path or URL to the stored document (e.g., S3 URL, local path) | Not Null                       |
| `mime_type`       | String     | E.g., `image/jpeg`, `application/pdf`                     |                                |
| `upload_status`   | Enum       | `PENDING_REVIEW`, `APPROVED`, `NEEDS_CORRECTION`          |                                |
| `uploaded_by_id`  | UUID/INT   | Foreign Key to `User` (the uploader)                        |                                |
| `uploaded_at`     | DateTime   | Timestamp of upload                                         | Auto-generated                 |
| `updated_at`      | DateTime   | Last update timestamp                                       | Auto-generated                 |

#### 2.7. Comment
For coordinator feedback on documents or information, or general notes on a step.

| Attribute         | Type       | Description                                                 | Notes                          |
| :---------------- | :--------- | :---------------------------------------------------------- | :----------------------------- |
| `id`              | UUID/INT   | Unique identifier for the comment                           | Primary Key                    |
| `step_instance_id`| String     | Identifier of the related embedded application step        | Matches `ApplicationStep.instance_id` |
| `document_id`     | UUID/INT   | Identifier of the related document                          | Can be null if targeting step/info |
| `author_id`       | UUID/INT   | Foreign Key to `User` (the coordinator)                     |                                |
| `content`         | Text       | The comment text                                            | Not Null                       |
| `created_at`      | DateTime   | Timestamp of comment creation                               | Auto-generated                 |

#### 2.8. ExternalStatusUpdate
Records manual updates from the coordinator regarding the external immigration process.

| Attribute       | Type       | Description                                                 | Notes                          |
| :-------------- | :--------- | :---------------------------------------------------------- | :----------------------------- |
| `id`            | UUID/INT   | Unique identifier for the status update                     | Primary Key                    |
| `application_id`| UUID/INT   | Foreign Key to `Application`                                |                                |
| `coordinator_id`| UUID/INT   | Foreign Key to `User` (the coordinator who added the update) |                                |
| `status_text`   | String     | E.g., "Application submitted to immigration", "Visa granted" | Not Null                       |
| `update_date`   | Date       | Date of the status update                                   |                                |
| `created_at`    | DateTime   | Timestamp of record creation                                | Auto-generated                 |

---

### 3. Relationships

```mermaid
erDiagram
    USER ||--o{ APPLICATION : "applicant_id"
    USER ||--o{ WORKFLOW : "coordinator_id"
    WORKFLOW ||--o{ APPLICATION : "workflow_id"
    APPLICATION ||--o{ DOCUMENT : "application_id"
    APPLICATION ||--o{ COMMENT : "application_id"
    APPLICATION ||--o{ EXTERNAL_STATUS_UPDATE : "application_id"
    DOCUMENT ||--o{ COMMENT : "document_id"
    USER ||--o{ DOCUMENT : "uploaded_by_id"
    USER ||--o{ COMMENT : "author_id"
    USER ||--o{ EXTERNAL_STATUS_UPDATE : "coordinator_id"

    USER {
        string id PK
        string name
        string email UK
        string role
        datetime created_at
        datetime updated_at
    }

    APPLICATION {
        string id PK
        string workflow_id FK
        string applicant_id FK
        string visa_type
        string target_country
        string status
        string title
        string steps
        datetime created_at
        datetime updated_at
    }

    WORKFLOW {
        string id PK
        string name
        string version
        string coordinator_id FK
        string description
        string metadata
        string steps
        datetime created_at
        datetime updated_at
    }

    DOCUMENT {
        string id PK
        string application_id FK
        string step_instance_id
        string file_name
        string storage_path
        string mime_type
        string upload_status
        string uploaded_by_id FK
        datetime uploaded_at
        datetime updated_at
    }

    COMMENT {
        string id PK
        string application_id FK
        string step_instance_id
        string document_id FK
        string author_id FK
        string content
        datetime created_at
    }

    EXTERNAL_STATUS_UPDATE {
        string id PK
        string application_id FK
        string coordinator_id FK
        string status_text
        date update_date
        datetime created_at
    }
```

---

### 4. Relationship Summary

*   `Workflow` stores embedded step definitions in its `steps` JSON array.
*   `Workflow` is owned by one coordinator via `coordinator_id`.
*   `Application` stores embedded step instances in its `steps` JSON array.
*   `Application` is owned by one applicant and instantiated from one workflow.
*   `Document` and `Comment` remain separate entities to preserve file storage and threaded feedback.
*   `Application` can have many `Document` records and many `Comment` records.
*   A `Document` may have many comments, and a `Comment` may optionally target a specific `Document`.
*   `ExternalStatusUpdate` records belong to one `Application` and are created by a coordinator.

---

### 5. Embedded step model notes

*   `WorkflowStep` and `ApplicationStep` are represented as embedded JSON objects, not as standalone persisted tables in this model.
*   `WorkflowStep` defines rendering metadata, instructions, input configuration, and order.
*   `ApplicationStep` contains the runtime answers, status, and references to `document_ids` and `comment_ids`.
*   `Document` and `Comment` are linked contextually via `step_instance_id` and `application_id`.

---

| « [Prev](./1-workflow-architecture.md) | [🏠︎](../README.md) | [Next](./3-api-design.md) » |
| --- | --- | --- |