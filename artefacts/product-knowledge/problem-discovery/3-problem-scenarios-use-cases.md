| « [Prev](./2-user-journey-map.md) | [🏠︎](../README.md) | [Next](./4-mvp-storyboard.md) » |
| --- | --- | --- |

---

# 🧩 Problem Scenarios & Use Cases

![problem-discovery-banner](../../banners/problem-discovery-banner.png)

<br/>

## 1. Problem Scenarios

Narrative descriptions of real situations that occur during visa preparation. These scenarios highlight pain points, user behaviours, and system requirements.

### 1.1 Blurry or Low-Quality Passport Photo

| Situation | Applicant takes a passport photo at night using their phone and sends it via a messaging app. |
|:-----------|:--------------------------------------------------------------------------------------------|
| Pain Points | - Coordinator cannot use the image.<br/>- Applicant doesn’t understand why it’s unacceptable.<br/>- Multiple rounds of resubmission. |
| Implication for Product | - Clear document requirements.<br/>- Ability to request corrections with notes.<br/>- Support for re-uploading. |

---

### 1.2 Applicant Forgets Which Step They Are On

| Situation | Applicant sends some documents but forgets others, unsure what remains. |
|:-----------|:------------------------------------------------------------------------|
| Pain Points | - Confusion.<br/>- Coordinator must remind them manually. |
| Implication for Product | - Visible progress tracker.<br/>- Clear next-step guidance. |

---

### 1.3 Coordinator Receives Documents in the Wrong Order

| Situation | Applicant sends documents randomly (passport, then employment letter, then photo). |
|:-----------|:----------------------------------------------------------------------------------|
| Pain Points | - Coordinator must reorganise manually.<br/>- Hard to track what belongs to which requirement. |
| Implication for Product | - Step-based document upload.<br/>- Structured workflow. |

---

### 1.4 Applicant Sends Incomplete Information

| Situation | Applicant answers some questions but skips others. |
|:-----------|:----------------------------------------------------|
| Pain Points | - Coordinator must chase missing details.<br/>- Applicant doesn’t know what’s required. |
| Implication for Product | - Required fields.<br/>- Validation before submission. |

---

### 1.5 Coordinator Requests Corrections but Applicant Is Busy

| Situation | Coordinator sends correction notes, but applicant doesn’t see them immediately. |
|:-----------|:--------------------------------------------------------------------------------|
| Pain Points | - Delays.<br/>- Coordinator must follow up. |
| Implication for Product | - Notifications.<br/>- Clear correction loop. |

---

### 1.6 External Authority Requests Additional Documents

| Situation | After submission, immigration asks for extra evidence. |
|:-----------|:---------------------------------------------------------|
| Pain Points | - No structured way to collect new documents.<br/>- Applicant confused about urgency. |
| Implication for Product | - Temporary steps.<br/>- External status updates. |

---

## 2. Use Cases

Structured descriptions of how users interact with the system.

---

### 2.1 Use Case: Applicant Uploads Required Document

| Actor | Applicant |
|:-------|:-----------|
| Trigger | Applicant opens a step requiring document upload. |
| Goal | Provide the correct document. |
| Steps | 1. Applicant opens the step.<br/>2. Reads instructions.<br/>3. Uploads document.<br/>4. Submits the step. |
| Success Outcome | Document submitted for coordinator review. |
| Failure Outcome | - Missing file.<br/>- Wrong file type.<br/>- Poor quality image. |

---

### 2.2 Use Case: Coordinator Reviews a Step

| Actor | Coordinator |
|:-------|:-------------|
| Trigger | Applicant submits a step. |
| Goal | Approve or request corrections. |
| Steps | 1. Coordinator opens submitted step.<br/>2. Reviews answers and documents.<br/>3. Adds notes if needed.<br/>4. Approves or requests changes. |
| Success Outcome | Step approved. |
| Failure Outcome | Applicant must correct and resubmit. |

---

### 2.3 Use Case: Applicant Corrects a Step

| Actor | Applicant |
|:-------|:-----------|
| Trigger | Coordinator requests changes. |
| Goal | Fix issues and resubmit. |
| Steps | 1. Applicant reads coordinator notes.<br/>2. Updates fields or re-uploads documents.<br/>3. Resubmits. |
| Success Outcome | Step moves back to coordinator. |
| Failure Outcome | Applicant misunderstands instructions. |

---

### 2.4 Use Case: Coordinator Tracks External Status

| Actor | Coordinator |
|:-------|:-------------|
| Trigger | Visa authority updates application status. |
| Goal | Record and communicate status. |
| Steps | 1. Coordinator receives update.<br/>2. Logs status in system.<br/>3. Applicant sees update. |
| Success Outcome | Applicant stays informed. |
| Failure Outcome | Applicant remains anxious or uninformed. |

---

### 2.5 Use Case: Applicant Views Progress

| Actor | Applicant |
|:-------|:-----------|
| Trigger | Applicant wants to know what’s left. |
| Goal | Understand remaining steps. |
| Steps | 1. Applicant opens progress view.<br/>2. Sees completed, pending, and correction steps. |
| Success Outcome | Applicant feels confident. |
| Failure Outcome | Applicant remains confused. |

---

## 3. Summary

These scenarios and use cases ensure the product supports real-world behaviours and challenges. They validate the MVP scope and prepare the foundation for the next discovery document: MVP Storyboarding.

---

| « [Prev](./2-user-journey-map.md) | [🏠︎](../README.md) | [Next](./4-mvp-storyboard.md) » |
| --- | --- | --- |