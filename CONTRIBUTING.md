# Contributing to the Equilibria Cognitive Concierge

First, thank you for considering contributing to this project. Every contribution, from a line of code to a line of feedback, is an act of forging a more intelligent and humane interface to knowledge.

This document outlines the unique development philosophy and collaborative protocols we use in this project. Adherence to these principles is essential for maintaining the velocity, quality, and architectural integrity of our work.

## The Development Philosophy: The Forge

We view the development of this application as an act of **forging**. It is a process that requires heat, pressure, and relentless refinement to transform raw materials (ideas, code) into a strong, resilient, and beautiful final product.

Our collaboration is structured around a partnership between two key roles:

1.  **The Architect (The Human Visionary):** The ultimate source of truth, strategic direction, and quality control. The Architect's primary role is to provide the vision and to execute the **`Socratic Stress-Test`** on all forged artifacts.
2.  **The AI Advisor (The AI Forger):** The master forger and proactive co-architect, responsible for translating the Architect's vision into production-grade artifacts (code, documentation, plans) and for identifying potential architectural flaws.

## The Supreme Workflow: The Mandate for Verifiable Truth

All contributions to this project follow a rigorous, four-step protocol. This workflow is designed to eliminate ambiguity, prevent errors, and ensure that every artifact is ratified and verified before we build upon it.

#### **1. Propose**

The AI Advisor forges a detailed, actionable proposal. This can be a prompt for the Dyad environment, a block of code for direct infusion, or a strategic document like an ADR. The proposal must be clear, specific, and directly aligned with the project's ratified PRD.

#### **2. Execute**

The Architect, in their capacity as the operator of the forge (the Dyad environment), executes the directive precisely as specified. Upon completion, the Architect confirms the action with the command **"Done."**

#### **3. Verify & Synthesize (MANDATORY)**

This is the most critical step. Upon the Architect's confirmation, the AI Advisor performs a mandatory **`Live Sync`** with the GitHub repository.

*   The AI Advisor analyzes the committed changes to verify that the directive was executed with perfect fidelity.
*   The AI Advisor generates a formal **"Verification Report,"** stating whether the execution was a `SUCCESS` or `FAILURE` and providing a detailed analysis.
*   **The Oracle Clause:** If the AI Advisor encounters ambiguity or has low confidence in its analysis of the codebase, it is constitutionally required to invoke the Oracle Clause. It will present the raw data it has observed and ask the Architect for a definitive verification of ground truth. Fabricating or "hallucinating" an answer is a critical failure of this protocol.

#### **4. Proceed**

Development proceeds to the next task **only** after a successful and verified report. If the verification fails, the immediate next action is to forge a corrective infusion to bring the codebase back into a state of **`Perfect Concordance`**.

## The Socratic Stress-Test

The Architect's most vital role is the execution of the Socratic Stress-Test. This is the act of asking hard, critical questions of every proposal and every forged artifact.

*   "Is this architecture truly scalable?"
*   "Does this UI decision create unnecessary friction?"
*   "Is there a simpler, more elegant solution?"

This process of rigorous, respectful questioning is not an act of opposition; it is the fire that tempers the steel. It is the single most important mechanism we have for ensuring quality and preventing architectural decay.

## The Development Workflow: From Sprint to Deployment

To ensure a structured, transparent, and efficient development process, this project follows a simple Agile methodology based on Sprints and Tasks. All tracking is managed directly within GitHub, leveraging Dyad's native integration.

### **Part 1: Sprints (GitHub Milestones)**

A **Sprint** is a time-boxed period (e.g., one week) during which a specific set of tasks is completed. In our forge, Sprints are managed as **GitHub Milestones**.

*   **Sprint Planning:** At the beginning of a Sprint, the Architect will create a new Milestone in the `webapp` GitHub repository, giving it a clear name (e.g., "Sprint 2: RAG Pipeline & Admin UI") and a due date.
*   **Task Allocation:** The Architect will then assign a curated list of GitHub Issues to this Milestone. The goal is to have a clear, achievable set of objectives for the Sprint.

### **Part 2: Tasks (GitHub Issues)**

Every discrete piece of work, from a new feature to a bug fix, must be tracked as a **GitHub Issue**. This ensures every change is documented and has a clear purpose.

*   **Creating a Task:**
    1.  The Architect will create a new Issue in the `webapp` GitHub repository.
    2.  **Title:** The title must be clear and concise (e.g., "Forge the User Profile Card on the Account Page").
    3.  **Description:** The description is critical. It must follow this structure:
        *   **User Story:** A brief, one-sentence description of the goal (e.g., "As a user, I want to be able to update my display name.").
        *   **PRD Reference:** A direct link to the relevant section(s) of the `/docs/PRD.md` file using Markdown anchors. This is non-negotiable.
        *   **Acceptance Criteria:** A clear, bulleted list of conditions that must be met for the task to be considered "Done."

### **Part 3: The Step-by-Step Workflow (Dyad & GitHub)**

This is the day-to-day protocol for development.

1.  **Task Selection:** The Architect selects an Issue from the current Sprint Milestone to work on.
2.  **Branch Creation:** Within GitHub, a new feature branch is created from `main`, named according to the Issue number (e.g., `feature/issue-12-user-profile-card`).
3.  **The Forging Loop (in Dyad):**
    *   The Architect checks out the new branch.
    *   The Architect provides the Dyad AI with the **Supreme Directive**, referencing the PRD sections detailed in the GitHub Issue.
    *   The Architect and the AI collaborate, following our **"Propose -> Execute -> Verify -> Proceed"** protocol to forge the feature.
4.  **Committing the Work (in Dyad):**
    *   Once a logical chunk of work is complete and verified, the Architect uses Dyad's **"Publish"** button.
    *   The commit message **must** reference the GitHub Issue number (e.g., `feat: Forge user profile form components #12`). This automatically links the commit to the task.
5.  **Opening a Pull Request (in GitHub):**
    *   When all work for the Issue is complete, the Architect opens a Pull Request (PR) from the feature branch to `main`.
    *   The PR description must use the GitHub keyword "Closes #12" or "Fixes #12". This will automatically link the PR to the Issue and will close the Issue when the PR is merged.
6.  **Code Review & Merge:**
    *   All PRs must be reviewed.
    *   Upon approval, the PR is merged into the `main` branch. This completes the task.

### **The Role of the AI in This Workflow**

The AI Advisor's role is to facilitate this process. When given a directive that references a GitHub Issue, the AI should:

*   Acknowledge the Issue number.
*   Use the PRD links within the Issue as its primary context.
*   Generate code that meets all acceptance criteria.
*   Generate conventional commit messages that correctly reference the Issue number.

## How to Contribute

All contributions, whether from the core team or the community, should align with this philosophy.

1.  **For Feature Proposals:** Open a new GitHub Issue. Frame your proposal with the same rigor as our PRD. Define the "what" and the "why."
2.  **For Code Contributions:**
    *   Fork the repository and create a new feature branch.
    *   Ensure your code adheres strictly to the project's `AI_RULES.md`.
    *   Submit a Pull Request. The description of your PR must explain how your contribution aligns with the ratified PRD and withstands a Socratic Stress-Test.

Thank you for joining us in the forge. Let us build something that endures.