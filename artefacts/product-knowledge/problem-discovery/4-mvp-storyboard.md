| « [Prev](./3-problem-scenarios-use-cases.md) | [🏠︎](../README.md) | Next » |
| --- | --- | --- |

---

# 🧪 MVP Storyboard

This storyboard illustrates the **end-to-end experience** of the MVP for both the **Applicant** and the **Coordinator**. It visualises the “happy path” — the simplest, most successful flow — without adding complexity or non‑MVP features.

The goal is to show how the MVP works as a guided, linear workflow.

---

## Applicant Storyboard

| Step                 | Applicant Actions                                                                 | Status / Notes                                             |
|----------------------|----------------------------------------------------------------------------------|------------------------------------------------------------|
| Access & Onboarding   | Receives link or access code, opens app on mobile, sees welcome screen           | No login, no account creation, immediate access            |
| Home Screen          | Views linear list of steps with status and progress bar                          | Status: Not Started, In Progress, Submitted, Needs Correction, Approved |
| Completing a Step     | Taps step, sees instructions, uploads documents, answers questions, submits      | Minimal fields, clear instructions, mobile-friendly upload |
| Receiving Corrections | Sees "Needs Correction" status, views coordinator notes, fixes and resubmits     | Reduces embarrassment and confusion                         |
| Completing All Steps  | Sees confirmation all steps completed, coordinator prepares visa submission      | Can still view all steps                                    |
| External Status Updates | Views manual status updates from coordinator                                    | Timeline: Application submitted, Biometrics requested, Visa granted |

---

## Coordinator Storyboard

| Step                   | Coordinator Actions                                                             | Status / Notes                                             |
|------------------------|--------------------------------------------------------------------------------|------------------------------------------------------------|
| Coordinator Dashboard  | Views list of applications with progress and steps needing review               | Single coordinator only                                    |
| Reviewing Submitted Steps | Opens application, reviews submitted steps, approves or requests changes       | Reduces cognitive load                                     |
| Managing Corrections    | Views steps needing correction, reviews resubmissions, approves when correct    | No complex messaging, notes only                           |
| Preparing for Submission | Sees all steps approved, downloads documents, submits visa externally          | No integration with immigration systems                    |
| Updating External Status | Manually adds status updates, applicant sees updates instantly                  | Examples: Submitted to immigration, Additional documents requested, Visa granted |

---

## Storyboard Summary

This storyboard shows the simplest version of the product that still delivers value:

* Applicants follow a clear, linear workflow.
* Coordinators review and approve steps.
* Corrections are structured and visible.
* External status is tracked manually.

This storyboard is the final step in Product Discovery before moving into **Solution Design** (data model, UI flows, API design).

You are now ready to transition into design and implementation.

---

| « [Prev](./3-problem-scenarios-use-cases.md) | [🏠︎](../README.md) | Next » |
| --- | --- | --- |