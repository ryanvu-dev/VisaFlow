| « Prev | [🏠︎](../README.md) | [Next](./2-data-model.md) » |
| --- | --- | --- |

---

# 🔄 Workflow Architecture

![solution-design-banner](../../banners/solution-design-banner.png)

<br/>

## 1. Overview

The workflow architecture defines the internal logic of the visa-preparation system. It describes how workflows are structured, how applications are instantiated, how applicants and coordinators interact, and how progress is tracked both internally and with external authorities.

The goals of this architecture are:

* Provide a clear, linear sequence of steps for applicants
* Give coordinators a structured review and tracking system
* Support universal workflows with visa-specific variations
* Maintain simplicity, predictability, and low cognitive load
* Ensure every step has a clear state, requirements, and outcomes

---

## 2. Core Entities

These entities form the backbone of the system. They separate workflow definitions from application instances and roles from data, ensuring scalability and clarity.

In this system, attributes represent the data or state an entity holds, while behaviours represent the actions or responsibilities the entity performs based on those attributes and context. Behaviours depend on attributes to function, but attributes do not strictly determine behaviours. This distinction helps clarify the roles and capabilities of each entity within the workflow.

### 2.1 User

A person who interacts with the system, either as an applicant or coordinator.

| Attribute | Description | 
| --- | --- | 
| user_id | Unique identifier for the user | 
| name | Full name of the user | 
| contact | Email or phone contact information | 
| role | User role (applicant or coordinator) | 

#### Behaviours

* **authenticate:** Verify user identity
* **access workflow instances based on role:** Access workflows according to user permissions
* **perform role-specific actions:** Execute actions permitted by the user's role

### 2.2 Workflow

A form or definition of the sequence and structure of steps for visa application processing.

| Attribute | Description | 
| --- | --- | 
| workflow_id | Unique identifier for the workflow | 
| name | Name of the workflow (e.g., "Universal Skeleton", "Subclass 600") | 
| version | Version number of the workflow | 
| steps | Ordered list of embedded WorkflowStep definitions | 
| metadata | Metadata such as language and region | 

#### Behaviours

* **instantiate:** Creates an Application from the workflow
* **validate structure:** Validates the workflow's structure and consistency
* **support versioning:** Supports multiple versions of the workflow

### 2.3 Application (Workflow Instance)

A real application created from a Workflow. This is what the applicant fills and the coordinator reviews.

| Attribute | Description | 
| --- | --- | 
| application_id | Unique identifier for the application instance | 
| workflow_id | Reference to the workflow | 
| applicant_id | Reference to the applicant user | 
| coordinator_id | Reference to the coordinator user | 
| steps | Ordered list of embedded StepInstance objects within this application | 
| external_statuses | List of ExternalStatus entities tracking progress with authorities | 
| overall_status | Current overall status (e.g., in progress, submitted, completed) | 

#### Behaviours

* track progress
* store applicant data
* store documents
* store coordinator notes
* update external authority progress

### 2.4 WorkflowStep

Defines the structure of a step inside a Workflow. Steps are embedded within the Workflow entity and do not have a separate step_id attribute.

| Attribute | Description | 
| --- | --- | 
| title | Title of the step | 
| description | Description of the step | 
| order_index | Position of the step in the workflow sequence | 
| step_input_components | Collection of input components including fields/questions, required documents, and validation rules | 

#### Behaviours

* define title, description, requirements
* define validation logic

This entity represents the step input components such as fields/questions, required documents, and validation rules.

### 2.5 ApplicationStep

A runtime occurrence of a Step within an Application. ApplicationStep is embedded within the Application entity and uniquely identified by its position in the application's steps list and its reference to the Workflow step definition. It stores user responses and references to separate document/comment entities.

| Attribute | Description | 
| --- | --- | 
| step_answers | List of answers provided for this step instance | 
| document_ids | List of document IDs associated with this step instance | 
| comment_ids | List of comment IDs associated with this step instance | 
| status | Current status (Not started → Approved) | 
| coordinator_notes | Notes from the coordinator | 

#### Behaviours

* submit
* request_correction
* approve
* resubmit

### 2.6 Step Input Component

Defines the input elements that structure data collection within a WorkflowStep. This entity details the types of inputs, validation rules, and metadata necessary for user interaction.

| Attribute | Description | 
| --- | --- | 
| component_id | Unique identifier for the input component | 
| type | Type of input (e.g., text, dropdown, file upload, date) | 
| label | Display label for the input | 
| required | Boolean indicating if input is mandatory | 
| validation_rules | Rules to validate input (e.g., regex, range) | 
| conditional_logic | Logic to show/hide input based on other inputs | 
| help_text | Additional guidance for the user | 
| order_index | Position of the input within the step | 

#### Behaviours

* validate input
* enforce required fields
* apply conditional display logic
* provide user guidance

### 2.7 Document

A document uploaded by a user as part of an Application. Documents are linked to the Application and associated StepInstance contextually.

| Attribute | Description | 
| --- | --- | 
| document_id | Unique identifier for the document | 
| application_id | Reference to the owning application | 
| uploaded_by | User ID of the uploader | 
| file_metadata | Metadata about the file (name, type, size) | 
| timestamp | Upload timestamp | 
| status | Current status (submitted, needs correction, approved) | 

#### Behaviours

* upload
* replace
* mark as approved / needs correction

### 2.8 Catalogue

A reusable collection or library of standardised items such as document types, visa subclasses, or input component templates that can be referenced across workflows or steps.

| Attribute | Description | 
| --- | --- | 
| catalogue_id | Unique identifier for the catalogue | 
| name | Name of the catalogue | 
| description | Description of the catalogue's purpose | 
| item_type | Type of items contained (e.g., document types, visa subclasses) | 
| items | List of catalogue items or references | 
| version | Version number for managing updates | 

#### Behaviours

* add_item
* remove_item
* update_item
* version_control
* provide_items_for_reference

### 2.9 ExternalStatus

Represents progress with the real visa authority.

| Attribute | Description | 
| --- | --- | 
| external_status_id | Unique identifier for the external status record | 
| application_id | Reference to the related application | 
| status_type | Type of status (submitted, biometrics requested, decision made) | 
| timestamp | Timestamp of the status update | 
| notes | Additional notes or comments | 

#### Behaviours

* update by coordinator
* notify applicant

---

## 3. Workflow Model

The Workflow Model defines the conceptual and structural framework that governs how workflows are created, managed, and executed within the visa-preparation system. It builds upon the Core Entities by specifying the relationships, rules, and constraints that ensure workflows are consistent, adaptable, and scalable.

### 3.1 Purpose

* Establish a clear schema for workflow templates and instances
* Define how steps are sequenced and interrelated
* Support variations and conditional paths within workflows
* Enable versioning and lifecycle management of workflows
* Facilitate integration with external systems and authorities

### 3.2 Key Concepts

| Concept | Description | 
| --- | --- | 
| Workflow | A form defining the ordered steps and rules for a visa application process |
| Application | A live instance of a workflow filled with applicant responses |
| Step | A discrete unit of work or data collection within a workflow | 
| StepInstance | A runtime occurrence of a Step within an Application | 
| Transition | The movement or progression from one Step to another based on conditions | 
| Condition | Logical expressions that determine workflow branching or step activation | 

### 3.3 Structure

The Workflow Model is composed of interconnected components:

* **Workflow:** Defines the ordered sequence of Steps, including metadata and versioning.
* **Steps:** Each Step contains input components and validation rules.
* **Transitions:** Define how and when the workflow moves from one Step to the next.
* **Conditions:** Control branching logic and dynamic workflow paths.
* **Applications:** Instances of a Workflow with runtime data and statuses.

### 3.4 Versioning and Lifecycle

Workflows support versioning to allow updates and improvements without disrupting active applications. Each version maintains backward compatibility and clear migration paths.

### 3.5 Integration Points

The Workflow Model includes hooks for integration with external authorities, document management systems, and notification services to ensure seamless end-to-end processing.

---

## 4. Step Anatomy

The Step Anatomy section details the internal structure and components of a Step within a Workflow. It breaks down the elements that define a Step's purpose, inputs, validations, and interactions.

### 4.1 Purpose

* Define the detailed structure of a Step
* Specify input components and their configurations
* Establish validation and conditional logic within the Step
* Support user interaction and data collection

### 4.2 Components

| Component | Description | 
| --- | --- | 
| Title | The display title of the Step | 
| Description | A detailed explanation or instructions for the Step | 
| Input Components | Fields, questions, or document requirements that collect data | 
| Validation Rules | Rules that enforce data integrity and completeness | 
| Conditional Logic | Logic to show or hide inputs based on user responses | 
| Help Text | Additional guidance to assist the user | 

### 4.3 Input Components

Input Components are the building blocks of a Step, defining the data to be collected. They include various types such as text fields, dropdowns, date pickers, file uploads, and more.

| Attribute | Description | 
| --- | --- | 
| component_id | Unique identifier for the input component | 
| type | Type of input (e.g., text, dropdown, file upload) | 
| label | Display label for the input | 
| required | Boolean indicating if the input is mandatory | 
| validation_rules | Rules to validate the input (e.g., regex, range) | 
| conditional_logic | Logic to show or hide the input based on other inputs | 
| help_text | Additional guidance for the user | 
| order_index | Position of the input within the Step | 

### 4.4 Validation and Conditional Logic

Validation rules ensure data correctness and completeness, while conditional logic dynamically adjusts the Step based on user inputs.

### 4.5 Behaviours

* Validate inputs according to rules
* Enforce required fields
* Apply conditional display logic
* Provide user guidance and help

---

## 5. State Machine

The State Machine section defines the mechanism by which the workflow and application states transition based on events, conditions, and user actions. It ensures that the workflow progresses logically and consistently through its lifecycle.

### 5.1 Purpose

* Define the states an application or step can be in
* Specify valid transitions between states
* Enforce rules and conditions for state changes
* Support rollback, correction, and approval processes

### 5.2 Key Concepts

| Concept | Description | 
| --- | --- | 
| State | A distinct status representing the current condition of an application or step | 
| Transition | A permitted change from one state to another triggered by an event or condition | 
| Event | An action or occurrence that triggers a state transition | 
| Condition | Logical criteria that must be met for a transition to occur | 

### 5.3 States

The State Machine defines a set of states for both the overall application and individual steps. Common states include:

* Not Started
* In Progress
* Submitted
* Under Review
* Approved
* Rejected
* Needs Correction

### 5.4 Transitions

Transitions define how and when the system moves from one state to another. Examples include:

* Submit: Not Started → Submitted
* Approve: Under Review → Approved
* Request Correction: Under Review → Needs Correction
* Resubmit: Needs Correction → Submitted

### 5.5 Rules and Conditions

Transitions are governed by rules and conditions to ensure valid state changes. For example, a step cannot be approved unless all required inputs and documents are complete and valid.

### 5.6 Behaviours

* Enforce state transition rules
* Trigger notifications on state changes
* Log state changes for audit and tracking
* Support manual overrides by coordinators

---

## 6. Coordinator–Applicant Interaction Loop

The system is built around a predictable, structured loop between applicants and coordinators. Each step in the workflow follows this interaction pattern.

### 6.1 Applicant Action Cycle

1. Applicant opens a step.
2. Applicant fills required fields.
3. Applicant uploads required documents.
4. Applicant submits the step for review.

Once submitted, the step becomes **locked** for the applicant until the coordinator responds.

### 6.2 Coordinator Review Cycle

1. Coordinator reviews submitted data and documents.
2. Coordinator chooses one of:
   * **Approve** the step
   * **Request changes** with notes
3. If changes are requested, the step returns to the applicant with clear instructions.

### 6.3 Correction Cycle

1. Applicant reviews coordinator notes.
2. Applicant updates fields or re‑uploads documents.
3. Applicant resubmits the step.

This loop continues until the step is approved.

### 6.4 Application Completion Cycle

Once all steps are approved:

* Coordinator submits the real visa application to the authority.
* ExternalStatus updates begin.
* Applicant can track progress asynchronously.

---

## 7. Edge Cases

The system must gracefully handle irregular or unexpected situations.

### 7.1 Missing or Incorrect Documents

* Applicant uploads wrong file → coordinator marks as "needs correction".
* Applicant forgets a file → step cannot be submitted.
* Coordinator can optionally upload a document on behalf of the applicant.

### 7.2 Step Skipping or Conditional Steps

Some steps may be optional or conditional.

* Workflow defines conditions (e.g., "Only required if applicant has previous travel").
* Conditional steps appear or disappear dynamically.

### 7.3 Workflow Versioning

If a workflow is updated:

* Existing applications continue using their original version.
* New applications use the new version.

### 7.4 External Authority Delays or Changes

* Coordinator can update ExternalStatus at any time.
* Applicant sees a timeline of updates.
* If authority requests additional documents, a new temporary step can be injected.

### 7.5 Applicant or Coordinator Reassignment

* Coordinator may change mid‑process.
* Applicant may need help from another family member.
* Reassignment updates the application metadata but preserves history.

### 7.6 Application Withdrawal or Cancellation

* Applicant may withdraw before submission.
* Coordinator may cancel due to ineligibility.
* Application enters a terminal state but remains viewable.

---

| « Prev | [🏠︎](../README.md) | [Next](./2-data-model.md) » |
| --- | --- | --- |