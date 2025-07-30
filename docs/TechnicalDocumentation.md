# Project Sutra: Technical Documentation

**Version:** 2.0
**Date:** July 30, 2025

## 1\. Introduction

This document provides a detailed technical overview of Project Sutra. It is intended for software developers and architects, outlining the system's architecture, technology stack, component design, and data flow. The goal is to provide a comprehensive guide for maintaining, extending, and contributing to the project.

## 2\. Core Architectural Principles

Project Sutra is founded on several key architectural principles designed to ensure it is modular, scalable, and maintainable.

  * **Clean Architecture:** The system uses a layered architecture to separate concerns. The core business logic is independent of external frameworks and infrastructure, making the system highly testable and flexible.
  * **Domain-Driven Design (DDD):** We model the software around its core domain—the analysis and documentation of code. This is reflected in our Pydantic models and service definitions.
  * **Stateless & Standalone Operation:** The application is designed to be completely stateless. It holds no memory between runs and is configured entirely through external files, allowing any user to run it on any project without installation or complex setup.

## 3\. The Crew & Pipeline Vision

The central pattern in Project Sutra is the **"Assembly Line" workflow**, which is implemented using a pipeline of specialized AI Crews. This is a deliberate design choice to enforce a single responsibility principle at the macro level.

  * **Atomic Crews:** Each `Crew` is designed to perform one specific, atomic task (e.g., "Analyze Code Syntax," "Write Documentation from Analysis," "Evaluate Documentation Quality"). It is a self-contained unit with its own agents and tasks.
  * **Sequential Pipeline:** An **Orchestrator** manages the workflow, passing the output of one crew as the input to the next. This creates a highly predictable and traceable data flow. For example:
    1.  `CodeAnalyzerCrew` produces a `CodeAnalysisResult` (JSON).
    2.  `TechnicalWriterCrew` receives the `CodeAnalysisResult` and produces a `DocumentationDraft` (Markdown).
    3.  `ReviewerCrew` receives the `DocumentationDraft` and produces an `EvaluationReport` (JSON).
  * **Intermediate Outputs:** The output from each crew is saved before being passed to the next. This is crucial for debugging, as it allows developers to inspect the data at each stage of the pipeline to pinpoint issues.

## 4\. Project Folder Structure

The project follows a standard Python application layout, organized by architectural layer.

```
sutra/
├── .env
├── .gitignore
├── requirements.txt
└── README.md
|
├── src/
│   └── sutra/
│       ├── __init__.py
│       │
│       ├── app/
│       │   ├── __init__.py
│       │   └── main.py
│       │
│       ├── config/
│       │   ├── __init__.py
│       │   └── settings.py
│       │
│       ├── core/
│       │   ├── __init__.py
│       │   └── models.py
│       │
│       ├── domain/
│       │   ├── __init__.py
│       │   ├── agents.py
│       │   └── crews.py
│       │
│       ├── exceptions/
│       │   ├── __init__.py
│       │   └── custom_exceptions.py
│       │
│       ├── infrastructure/
│       │   ├── __init__.py
│       │   ├── file_handler.py
│       │   └── llm_client.py
│       │
│       └── utils/
│           ├── __init__.py
│           └── helpers.py
│
└── tests/
    ├── __init__.py
    ├── config/
    │   └── test_settings.py
    └── domain/
        └── test_crews.py
```

## 5\. Configuration Management

The system's standalone and stateless nature is achieved through a two-file configuration system. This separates environment-specific pointers and secrets from the main application configuration.

1.  **The `.env` File:** This file resides in the root of the Sutra project. It is minimal and contains only two types of information:

      * **Pointer to the main config file:** `CONFIG_FILE_PATH="/path/to/user/project/config.yaml"`
      * **Secrets:** Any required API keys or credentials for the user's chosen LLM service (e.g., `MY_LOCAL_LLM_API_KEY="secret-key"`).

2.  **The User's YAML Configuration File:** This is a comprehensive YAML file created by the user for their target project. It defines the behavior of Sutra for that project.

      * **Example (`my_project_config.yaml`):**
        ```yaml
        # Paths for the target project
        paths:
          input_code_directory: "/path/to/my/project/src"
          output_directory: "/path/to/my/project/docs/generated"

        # Rule definitions for auditing
        rules:
          coding_standards_file: "/path/to/my/project/coding_standards.yaml"

        # LLM provider settings (agnostic)
        llm:
          provider: "local_api" # or "openai", "gemini"
          api_base: "http://localhost:1234/v1"
          model_name: "local-model"
        ```

3.  **Loading Logic (`config/settings.py`):** The Pydantic settings loader in Sutra first reads the `.env` file to get the path to the YAML file and any secrets. It then reads and parses the YAML file, merging the two sources into a single, validated configuration object.

## 6\. Technology Stack

  * **Language:** Python 3.10+
  * **Core Framework:** CrewAI
  * **Data Validation & Settings:** Pydantic
  * **Configuration:** PyYAML
  * **LLM Interaction:** Agnostic. The `llm_client.py` will be a wrapper designed to interact with any OpenAI-compatible API endpoint. The user specifies the API base URL and model in their config file.
  * **Testing:** Pytest, pytest-cov, pytest-mock
  * **Code Quality:** Black, isort, flake8, mypy

## 7\. Component Breakdown (`src/sutra/`)

  * **`app/main.py`**: The main entrypoint. Initializes settings and starts the main orchestrator.
  * **`config/settings.py`**: Contains Pydantic models for loading and validating settings from both `.env` and the user's YAML file.
  * **`core/models.py`**: Defines core Pydantic models passed between crews (`CodeAnalysisResult`, `AuditReport`).
  * **`domain/agents.py` & `domain/crews.py`**: Contains the definitions for all specialized `Agent`s and the `Crew`s that compose them.
  * **`infrastructure/file_handler.py`**: Implements all file system read/write operations.
  * **`infrastructure/llm_client.py`**: A generic client for making API calls to the LLM endpoint specified in the user's configuration. It is not tied to any specific provider's SDK.

## 8\. Testing Strategy

  * Tests are located in the top-level `tests/` directory, mirroring the `src/sutra/` package structure.
  * **Unit Tests:** Components are tested in isolation. Dependencies on outer layers (like the file system or LLM client) are mocked using `pytest-mock` to ensure business logic is tested independently.
  * **Integration Tests:** A smaller suite of tests verifies the interaction between layers, ensuring, for example, that a crew can correctly use the `file_handler` to read a test file.