# Project Sutra: Functional Documentation

**Version:** 1.2
**Date:** July 30, 2025

---

## 1. Introduction

Project Sutra is an AI-powered software development assistant designed to enhance code quality and maintainability. It acts as an automated "charioteer" or guide (**Sarthi**) for developers by analyzing source code to generate comprehensive documentation and audit reports.

The system is designed to be **stateless and highly configurable**, integrating into a developer's existing workflow without requiring complex setup or storing any sensitive project information. Its primary function is to provide clear, actionable insights into a codebase, improving consistency and reducing technical debt.

## 2. Core Concepts

From a functional standpoint, the system operates on a few key concepts:

* **Crews:** A "Crew" is a specialized team of AI agents responsible for completing a single, well-defined task. For example, a `Code_Analyzer_Crew` is responsible only for analyzing code.
* **Assembly Line Workflow:** Project Sutra processes tasks sequentially using a pipeline of these specialized Crews. The output of one Crew becomes the input for the next, ensuring a structured and traceable workflow.
* **AI-Powered Evaluation:** The system includes a dedicated `Reviewer_Crew` that acts as an automated quality gate. It uses an LLM (Large Language Model) as a "judge" to evaluate the output of other crews against a predefined set of metrics.

## 3. The Unified Sutra Workflow

Project Sutra utilizes a standardized, multi-stage workflow for both new (greenfield) and existing (brownfield) projects. The entry point into the workflow differs, but the subsequent stages of review, annotation, and auditing are consistent, ensuring high-quality outcomes for any project.

---

### **Stage 1: Documentation Foundation**

This initial stage establishes the baseline documentation that will guide the rest of the process.

* **For New Projects:**
    * **Action:** The user provides a set of high-level requirements.
    * **Output:** Sutra generates a complete project blueprint, including **Functional, Non-Functional, and Technical Documentation** in Markdown format.

* **For Existing Projects:**
    * **Action:** The AI reads and analyzes the entire codebase from the user-specified local directory.
    * **Output:** Sutra reverse-engineers the project to produce the same set of foundational documents: **Functional, Non-Functional, and Technical Documentation**, along with detailed API docs and module summaries.

---

### **Stage 2: Human-in-the-Loop Review (Universal Feedback Step)**

This is the most critical stage for ensuring accuracy and alignment.

* **Action:** The developer (the user) reviews the complete set of documentation generated in Stage 1.
* **Process:** The developer makes corrections, adds clarifications, and refines the documentation to ensure it accurately reflects the *intended* functionality and business logic.
* **Output:** A set of **human-verified documentation files**. This corrected documentation now serves as the "ground truth" for all subsequent stages.

---

### **Stage 3: Development & Intelligent Code Annotation**

This stage focuses on creating or refining the source code based on the verified documentation.

* **For New Projects:**
    * **Action:** Developers write the initial source code, using the verified documentation from Stage 2 as their definitive guide.

* **For Existing Projects:**
    * **Action:** The AI re-processes the codebase, using the verified documentation as its primary context.
    * **Output:** The original source code files are updated with high-quality annotations, including **new or corrected inline comments** and **rewritten function/class docstrings**.

---

### **Stage 4: Architecture & Best Practices Audit (Universal)**

Once a codebase exists (either newly written or existing), this stage ensures it adheres to project standards.

* **Action:** The AI audits the codebase against the user-defined "rulebook" (`coding_standards.yaml`).
* **Process:** It checks for compliance with rules like folder naming, file structure, and dependency management.
* **Output:** A `structure_report.json` file containing a structured list of all violations, warnings, and suggestions.

---

### **Stage 5: Deep Optimization Audit (Universal)**

This final stage performs a deep analysis to identify potential performance and logical issues.

* **Action:** The AI performs a final, deep analysis of the code.
* **Process:** It uses the combined context of the **human-verified documentation (Stage 2)** and the **architectural rules (Stage 4)** to analyze the code's logic for inefficiencies. It looks for:
    * High code complexity (e.g., cyclomatic complexity).
    * Algorithmic inefficiencies (e.g., O(n²) loops).
    * Inconsistencies between the code's behavior and its verified documentation.
* **Output:** A `functional_review_report.json` file with detailed refactoring and optimization suggestions. For existing codebases, this audit benefits greatly from the rich, code-derived context established in Stage 1 and refined in Stage 3.

## 4. User Interaction Summary

1.  **Configure:** The user sets up a `.yaml` file defining project paths and a `.env` file for secrets.
2.  **Execute:** The user runs the Sutra workflow, indicating whether it's a new or existing project.
3.  **Review & Refine:** The user actively participates in the **Human-in-the-Loop Review** (Stage 2) to verify all documentation.
4.  **Utilize:** The user consumes the final outputs (annotated code, audit reports, and final documentation) to improve their project.