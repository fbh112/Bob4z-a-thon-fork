# Lab: Discovering IBM Bob Premium Package for Z
## Complete Analysis of the CBSA Application with Bob for Z

- **Estimated Duration:** 1-2 hours
- **Level:** Intermediate to Advanced
- **Prerequisites:** Completion of [Lab 1 — Discovering IBM Bob Premium Package for Z](1-LabDiscoverCBSA_EN.md)

---

## 📋 Table of Contents

1. [Introduction](#1-introduction)
2. [Lab Preparation](#2-lab-preparation)
3. [Exercise 1: Workspace Initialization](#exercise-1-workspace-initialization)
4. [Exercise 2: Frontend Inventory](#exercise-2-frontend-inventory)
5. [Exercise 3: Architecture Diagrams](#exercise-3-architecture-diagrams)
6. [Exercise 4: Functional Analysis — Local Transfer](#exercise-4-functional-analysis--local-transfer)
7. [Exercise 5: User Guide](#exercise-5-user-guide)
8. [Exercise 6: Technical Analysis — Credit Score](#exercise-6-technical-analysis--credit-score)
9. [Conclusion](#3-conclusion)

---

## 1. Introduction

[↩️](#-table-of-contents)

This lab continues from **Lab 1 — Discovering IBM Bob Premium Package for Z** and extends the analysis to the full CBSA application stack. You will reuse the same IBM Bob Premium Package for Z setup from Lab 1, now applied to the CBSA front-end components — Java, React, REST APIs, and z/OS Connect — alongside the mainframe back-end already explored in Lab 1.

This lab guides you through a complete analysis of the **CICS Banking Sample Application (CBSA)** using **IBM Bob Premium Package for Z**, the AI assistant specialized for IBM Z mainframe systems.

At the end of this lab, you will be able to:

✅ **Initialize** a multi-technology workspace (Java, React, COBOL, z/OS Connect)
✅ **Generate** a complete front-end architecture inventory
✅ **Create** global architecture diagrams integrating front-end and back-end layers
✅ **Analyze** complex CICS transactions end-to-end (from REST API to COBOL)
✅ **Write** user guides for end users from source code alone
✅ **Understand** the JCICS asynchronous API pattern
✅ **Use** Bob's different modes effectively (💻 Agent, 🧰 Z Code, 📐 Z Architect)

---

## 2. Lab Preparation

[↩️](#-table-of-contents)

### ✅ Prerequisites

| Requirement                                    | Details                                                             |
| ---------------------------------------------- | ------------------------------------------------------------------- |
| Lab 1 completed or reviewed                    | Familiar with Bob modes, Z Code and Z Architect                     |
| Bob IDE with IBM Bob Premium Package for Z     | Same versions as Lab 1                                              |
| Git client CLI                                 | Available in terminal                                               |
| Basic knowledge of Java, COBOL, REST APIs      | Recommended but not mandatory                                       |

### 📝 Setting Up the Workspace

1. Create a `lab2` directory in your home directory.
2. Open Bob IDE, click **File → Open Folder**, and select the `lab2` directory.
   > ⚠️ When prompted, click **Trust the authors** to avoid the IDE entering **Restricted** mode.
3. Open a terminal by choosing **Terminal → New Terminal**, then run:

```bash
git clone https://github.com/fbh112/CBSA-frontend-source.git
```

Once cloned, you will find the following subdirectories under your workspace:

```text
CBSA-frontend-source/
├── bank-application-frontend/                   # Banking frontend application (React)
├── webui/                                       # Web user interface
├── Z-OS-Connect-Customer-Services-Interface/    # z/OS Connect interface for customer services
├── Z-OS-Connect-Payment-Interface/              # z/OS Connect interface for payments
└── zosconnect_artefacts/                        # z/OS Connect service artifacts
```

Once your workspace is set up, click the **`+` icon** in the Bob panel header to start a new conversation.

> **Note:** IBM Bob relies on AI agentics, which is non-deterministic. Your results may differ slightly from the samples shown here. If Bob takes a different path, guide it with follow-up prompts — it will reach the correct outcome.

---

## Exercise 1: Workspace Initialization

[↩️](#-table-of-contents)

### 🎯 Objective

Initialize the workspace and create the `AGENTS.md` file that will serve as a persistent guide for Bob and developers across all future conversations.

### 🔧 Bob Mode to Use

**Mode: 🧰 Z Code**

Z Code mode is specialized for analyzing and documenting mainframe applications (COBOL, PL/I, JCL, Assembler, REXX).

### 📝 Context

You have the CBSA frontend project that you are discovering alongside the mainframe back-end. You need to:

- Understand the project structure across all technologies
- Identify the languages and frameworks used (Java, React, COBOL, z/OS Connect)
- Locate important files
- Build metadata that will facilitate project analysis and enable more relevant documentation

### ✍️ Your Task

Write your own prompt to ask Bob to initialize and fully analyze the workspace.

**Expected in your prompt:**
- Request workspace initialization
- Request the creation of framing artifacts (like `AGENTS.md`)

### 💬 Bob Prompt

```text
/init
```

> **Note:** `/init` is a **built-in Bob slash command**. It scans the project and generates persistent context files for Bob, creating `AGENTS.md` in the workspace root.
>
> This file gives Bob persistent knowledge of your project across all conversations, so it does not need to rediscover the codebase each time.
>
> You can find more built-in commands at: https://www.ibm.com/docs/en/bobz/3.0.0?topic=commands-using-tools-tool-groups

### 🔀 Prompt Variants

```text
Initialize this workspace and analyze the complete project structure.
```

```text
Analyze this mainframe repository, identify key components and prepare necessary framing files.
```

### ⚙️ What Bob Does Automatically

Bob will:

- Analyze the workspace (structure, languages, files)
- Detect present languages and frameworks
- Check for data dictionary existence
- Search for coding standards
- Locate technical documentation
- Map programs to their documentation
- Display detected configuration for confirmation
- Update `AGENTS.md` with non-obvious information

### ✅ Sample Result

`AGENTS.md` is created under the workspace root.

### ✍️ Update AGENTS.md — Set the Documentation Output Folder

Now that the workspace is initialized, tell Bob where all generated documentation should be saved. This ensures that every subsequent exercise stores its output consistently under `docs/`.

```text
Update @AGENTS.md to specify that all generated documentation, reports, and diagrams must be saved under the docs/ folder.
```

Bob will update `AGENTS.md` with a convention that it will follow in all future conversations, so you will not need to repeat the instruction in every prompt.

### 🎓 What You Learn

- **The importance of initialization:** The workspace is the starting point for all analyses. Some steps are essential before going further.
- **Time savings:** Instead of spending hours manually exploring the code, Bob analyzes everything in a few minutes.
- **Standardization:** Conventions are automatically established.
- **Living documentation:** The `AGENTS.md` file serves as a persistent reference for the entire team.

---

## Exercise 2: Frontend Inventory

[↩️](#-table-of-contents)

### 🎯 Objective

Document the complete frontend architecture including REST APIs, JSON models, and data access layers.

### 🔧 Bob Mode to Use

**Mode: 🧰 Z Code**

Z Code mode analyzes source code across languages and generates structured inventories.

### 📝 Context

Now that the workspace is initialized, you need a detailed inventory of the frontend components to understand how the browser-side application and the z/OS Connect integration layers interact with the COBOL back-end programs already analyzed in Lab 1.

### 💬 Bob Prompt

```text
Make an inventory of the front-end.
```

### ⚙️ What Bob Does Automatically

Bob will:

1. Analyze JAX-RS REST resources (5 resources)
2. Document all endpoints with HTTP methods
3. Analyze JSON models (13 classes)
4. Document the data access layer (8 classes)
5. Identify COBOL interfaces (8 classes)
6. Create complete structured documentation

### ✅ Sample Result

**File created:** `docs/FRONTEND_INVENTORY.md`

---

## Exercise 3: Architecture Diagrams

[↩️](#-table-of-contents)

### 🎯 Objective

Create architecture diagrams visualizing the global application architecture and the detailed frontend layers.

### 🔧 Bob Mode to Use

**Mode: 📐 Z Architect**

Z Architect mode is optimized for design tasks: architecture diagrams, dependency mapping, and system analysis.

### 📝 Context

The frontend inventory from Exercise 2 gives a textual picture of the components. A visual diagram is essential for communicating the full stack to architects, project managers, and new developers — showing how the React UI, REST APIs, z/OS Connect, and COBOL programs connect across layers.

### 💬 Bob Prompt

```text
Generate a global architecture diagram integrating the front-end and backend, distinguishing the different layers of the application.
```

### ⚙️ What Bob Does Automatically

Bob will analyze the workspace inventory and generate a Mermaid diagram saved to a Markdown file, showing all application layers and their dependencies.

### ✅ Sample Result

**File created:** `docs/CBSA_Architecture_Diagram.md`

**Expected content — 5 distinct layers:**

| # | Layer                    | Technologies                    |
| - | ------------------------ | ------------------------------- |
| 1 | **Presentation Layer**   | Carbon React UI, Spring Boot UI |
| 2 | **Application Layer**    | JAX-RS REST resources           |
| 3 | **Integration Layer**    | REST API via z/OS Connect       |
| 4 | **Business Logic Layer** | CICS COBOL programs             |
| 5 | **Data Access Layer**    | Db2, VSAM, COBOL interfaces     |

---

## Exercise 4: Functional Analysis — Local Transfer

[↩️](#-table-of-contents)

### 🎯 Objective

Understand in depth how the local funds transfer transaction works, from the frontend through to the COBOL backend.

### 🔧 Bob Mode to Use

**Mode: 🧰 Z Code**

Z Code mode excels at tracing end-to-end flows across source files of different languages and technologies.

### 📝 Context

The local transfer function is one of the core banking operations in CBSA. Understanding it end-to-end — from the REST API call in the Java layer, through z/OS Connect, to the COBOL programs executing the debit and credit — is a representative exercise in full-stack mainframe analysis.

### 💬 Bob Prompt

```text
What does the Transfer Local transaction consist of?
```

### ⚙️ What Bob Does Automatically

Bob will:

1. Read source files across the frontend and backend
2. Analyze the end-to-end transaction flow
3. Identify input validations performed
4. Document error handling
5. Explain the anti-deadlock strategy
6. Detail security mechanisms (`SYNCPOINT`, `ROLLBACK`)

### ✅ Sample Result

Bob produces a structured functional analysis covering the full transaction lifecycle — from the REST API call in `TransferLocalJSON.java`, through the CICS bridge, to the COBOL programs `XFRFUN` and `BNK1TFN` — including validation rules, error codes, and rollback behaviour.

---

## Exercise 5: User Guide

[↩️](#-table-of-contents)

### 🎯 Objective

Create a complete user guide for the local transfer function, intended for end users.

### 🔧 Bob Mode to Use

**Mode: ❓ Ask**

Ask mode is ideal for producing non-technical documentation intended for end users, written in accessible language.

### 📝 Context

Tellers and end users need a simple, step-by-step guide for performing a local transfer — covering all three available interfaces (BMS terminal, Web UI, REST API), with practical examples and troubleshooting tips.

### 💬 Bob Prompt

```text
Make me a user guide for the Local Transfer function.
```

### ⚙️ What Bob Does Automatically

Bob will:

1. Create a professional guide structure
2. Document prerequisites
3. Explain access via the 3 interfaces (BMS terminal, Web UI, REST API)
4. Detail the step-by-step transfer procedure
5. List all error messages with their solutions
6. Provide practical examples
7. Add FAQ and security tips

### ✅ Sample Result

**File created:** `docs/USER_GUIDE_LOCAL_TRANSFER.md`

---

## Exercise 6: Technical Analysis — Credit Score

[↩️](#-table-of-contents)

### 🎯 Objective

Understand the credit scoring system and its technical implementation using the JCICS asynchronous API.

### 🔧 Bob Mode to Use

**Mode: 🧰 Z Code**

Z Code mode is well suited for analyzing asynchronous processing patterns in COBOL programs that use the JCICS API.

---

### Exercise 6A: General Operation

#### 💬 Bob Prompt

```text
What does the Credit Score function do?
```

#### ✅ Sample Result

Bob explains the Credit Score function: it is invoked asynchronously via the JCICS API, calls multiple scoring sub-programs in parallel, aggregates their results, and stores the final score against the customer record in Db2.

---

### Exercise 6B: Value Scale

#### 💬 Bob Prompt

```text
What is the credit score value scale?
```

#### ✅ Sample Result

Bob responds with a structured breakdown including:

**1. Value Range:**

| Field   | Value   |
| ------- | ------- |
| Minimum | 1       |
| Maximum | 999     |
| Type    | Integer |

**2. Calculation formula:**

```text
Final Score = (Score1 + Score2 + Score3 + Score4 + Score5) / 5
```

---

## 3. Conclusion

[↩️](#-table-of-contents)

### 🎉 Congratulations!

You have completed the IBM Bob Premium Package for Z full-stack CBSA lab. Building on the mainframe workspace from Lab 1 and extending to the frontend, you have:

- Initialized a multi-technology workspace and established the `docs/` output convention
- Generated a complete **frontend architecture inventory** covering REST APIs, JSON models, and COBOL interfaces
- Created a **global architecture diagram** showing all 5 application layers from React UI to Db2
- Analyzed the **local transfer transaction** end-to-end across Java, z/OS Connect, and COBOL
- Written a **user guide** for the Local Transfer function from source code alone
- Understood the **JCICS asynchronous credit scoring** pattern

### 🔧 Mode Usage Summary

| Exercise                          | Mode Used      | Reason for Choice                                            |
| --------------------------------- | -------------- | ------------------------------------------------------------ |
| 1. Workspace Initialization       | 🧰 Z Code      | Workspace scan and `AGENTS.md` creation                      |
| 2. Frontend Inventory             | 🧰 Z Code      | Multi-language source analysis and inventory generation      |
| 3. Architecture Diagrams          | 📐 Z Architect | Full-stack diagram creation across layers                    |
| 4. Functional Analysis            | 🧰 Z Code      | End-to-end transaction flow tracing                          |
| 5. User Guide                     | ❓ Ask         | Non-technical documentation for end users                    |
| 6. Credit Score Analysis          | 🧰 Z Code      | Asynchronous JCICS pattern analysis                          |

### 📚 Created Resources

| Document                            | Content                          | Approximate Size |
| ----------------------------------- | -------------------------------- | ---------------- |
| `AGENTS.md`                         | Workspace reference guide        | —                |
| `docs/FRONTEND_INVENTORY.md`        | Complete frontend inventory      | ~600 lines       |
| `docs/CBSA_Architecture_Diagram.md` | 5-layer architecture diagram     | —                |
| `docs/USER_GUIDE_LOCAL_TRANSFER.md` | End-user guide for local transfer | ~500 lines       |

---

**Lab Version:** 2.0
**Date:** 2026-09
**Author:** IBM Bob Premium Package for Z Team
**Prerequisite:** [Lab 1 — Discovering IBM Bob Premium Package for Z](1-LabDiscoverCBSA_EN.md)
