# Lab: Complete Analysis of the CBSA Application with Bob for Z

## Lab Overview

This lab guides you through a complete analysis of the **CICS Banking Sample Application (CBSA)** using **IBM Bob Premium Package for Z**,
the AI assistant specialized for IBM Z mainframe systems.
As a complement to **1-LabDiscoverCBSA**, this lab additionally integrates the application front-end (Java, React, REST APIs, and z/OS Connect).
You will learn to use different Bob modes and formulate effective prompts to obtain detailed analyses, documentation, and architecture diagrams.

---

## Prerequisites

- ✅ Git client CLI
- ✅ Bob IDE with the **IBM Bob Premium Package for Z** extension installed
- ✅ Access to Bob's **Z Code** and **Z Architect** modes
- ✅ Basic knowledge of Java, COBOL, JCL, and mainframe architecture (recommended but not mandatory)
- ✅ Lab **1-LabDiscoverCBSA** completed or reviewed

---

## Estimated Duration

**Total time: 1–2 hours**

---

## Table of Contents

0. [Preparation: Retrieving the Application Front-end](#preparation-0)
1. [Exercise 1: Workspace Initialization](#exercise-1)
2. [Exercise 2: Frontend Inventory](#exercise-2)
3. [Exercise 3: Architecture Diagrams](#exercise-3)
4. [Exercise 4: Functional Analysis — Local Transfer](#exercise-4)
5. [Exercise 5: User Guide](#exercise-5)
6. [Exercise 6: Technical Analysis — Credit Score](#exercise-6)

---

<a name="preparation-0"></a>

## Preparation: Retrieving the Application Front-end

### 🎯 Objective

Retrieve the source code of the CBSA application's front-end from GitHub and prepare the workspace for the lab.

### 🔧 Bob Mode to Use

**Mode: 💻 Agent**

Agent mode allows executing system commands and manipulating files.

### 📝 Context

Before starting the analysis, you need to retrieve the CBSA frontend source code from a public GitHub repository.

**Steps:**

1. Create a `lab2` directory in your home directory.
2. Open Bob IDE, click **File > Open Folder**, and select the `lab2` directory.
   > ⚠️ When prompted, click **Trust the authors** to avoid the IDE entering **Restricted** mode.
   >
3. Open a terminal by choosing **Terminal → New Terminal**, then run:

```bash
git clone https://github.com/fbh112/CBSA-frontend-source.git
```

Once cloned, you will find the following subdirectories under your workspace:

```text
CBSA-frontend-source/
├── bank-application-frontend/              # Banking frontend application (React)
├── webui/                                  # Web user interface
├── Z-OS-Connect-Customer-Services-Interface/  # z/OS Connect interface for customer services
├── Z-OS-Connect-Payment-Interface/         # z/OS Connect interface for payments
└── zosconnect_artefacts/                   # z/OS Connect service artifacts
```

### 🎯 You're Ready!

Once your workspace is set up, click the **`+` icon** in the Bob panel header to start a new conversation.

---

<a name="exercise-1"></a>

## Exercise 1: Workspace Initialization

### 🎯 Objective

Initialize the workspace and create the `AGENTS.md` file that will serve as a guide for Bob and developers.
Create metadata from existing programs and resources, and generate the data dictionary.

### 🔧 Bob Mode to Use

**Mode: 🧰 Z Code**

Z Code mode is specialized for analyzing and documenting mainframe applications (COBOL, PL/I, JCL, Assembler, REXX).

### 📝 Context

You have the CBSA frontend project that you are discovering. You need to:

- Understand the project structure
- Identify the languages and frameworks used
- Locate important files
- Build metadata that will facilitate project analysis and generate a data dictionary that will enable more relevant documentation

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

<a name="exercise-2"></a>

## Exercise 2: Frontend Inventory

### 🎯 Objective

Document the complete frontend architecture including REST APIs, JSON models, and data access layers.

### 🔧 Bob Mode to Use

**Mode: 🧰 Z Code**

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

<a name="exercise-3"></a>

## Exercise 3: Architecture Diagrams

### 🎯 Objective

Create architecture diagrams visualizing the global application architecture and the detailed frontend layers.

### 🔧 Bob Mode to Use

**Mode: 📐 Z Architect**

Z Architect mode is optimized for design tasks: architecture diagrams, dependency mapping, and system analysis.

### 💬 Bob Prompt

```text
Generate a global architecture diagram integrating the front-end and backend, distinguishing the different layers of the application.
```

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

<a name="exercise-4"></a>

## Exercise 4: Functional Analysis — Local Transfer

### 🎯 Objective

Understand in depth how the local funds transfer transaction works, from the frontend through to the COBOL backend.

### 🔧 Bob Mode to Use

**Mode: 🧰 Z Code**

### 💬 Bob Prompt

```text
What does the Transfer Local transaction consist of?
```

### ⚙️ What Bob Does Automatically

Bob will:

1. Read source files
2. Analyze the end-to-end transaction flow
3. Identify input validations performed
4. Document error handling
5. Explain the anti-deadlock strategy
6. Detail security mechanisms (`SYNCPOINT`, `ROLLBACK`)

### ✅ Sample Result

Bob produces a structured functional analysis covering the full transaction lifecycle — from the REST API call in `TransferLocalJSON.java`, through the CICS bridge, to the COBOL programs `XFRFUN` and `BNK1TFN` — including validation rules, error codes, and rollback behaviour.

---

<a name="exercise-5"></a>

## Exercise 5: User Guide

### 🎯 Objective

Create a complete user guide for the local transfer function, intended for end users.

### 🔧 Bob Mode to Use

**Mode: 🧰 Z Code**

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

<a name="exercise-6"></a>

## Exercise 6: Technical Analysis — Credit Score

### 🎯 Objective

Understand the credit scoring system and its technical implementation using the JCICS asynchronous API.

### 🔧 Bob Mode to Use

**Mode: 🧰 Z Code**

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

## Deliverables Summary

At the end of this lab, you will have created the following files under `docs/`:


| # | File                                | Type                 | Approximate Size |
| - | ----------------------------------- | -------------------- | ---------------- |
| 1 | `docs/FRONTEND_INVENTORY.md`        | Inventory            | ~600 lines       |
| 2 | `docs/CBSA_Architecture_Diagram.md` | Architecture diagram | 5 layers         |
| 3 | `docs/USER_GUIDE_LOCAL_TRANSFER.md` | User guide           | ~500 lines       |

**Total: 3 files documenting the CBSA frontend architecture and key transactions.**

---

## Skills Acquired

At the end of this lab, you will know how to:

✅ Document frontend architectures
✅ Create professional architecture diagrams
✅ Analyze complex CICS transactions end-to-end
✅ Write detailed user guides for end users
✅ Understand the JCICS asynchronous API
✅ Effectively use Bob's different modes (Agent, Z Code, Z Architect)
✅ Formulate precise and effective prompts

---

## Conclusion

This lab allowed you to discover the power of IBM Bob Premium Package for Z in analyzing and documenting complex mainframe front-end applications. You learned to:

- Use different Bob modes for targeted tasks (Agent, Z Code, Z Architect)
- Use different types of prompts to obtain precise results
- Automatically generate professional documentation
- Create visual architecture diagrams with layered representations
- Understand complex CICS transactions end-to-end
- Document REST APIs and data models

---

**Lab Version:** 2.0
**Creation date:** 2026-09
**Author:** IBM Bob Premium Package for Z Team

---
