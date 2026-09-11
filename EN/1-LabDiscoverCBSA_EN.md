# Lab: Discovering IBM Bob Premium Package for Z
## Analysis and Documentation of a CICS Mainframe Application

- **Estimated Duration:** 2-3 hours
- **Level:** Intermediate to Advanced
- **Prerequisites:** Basic knowledge of COBOL and CICS

---

## 📋 Table of Contents

1. [Introduction](#1-introduction)
2. [IBM Bob Premium Package for Z Modes](#2-ibm-bob-premium-package-for-z-modes)
3. [Lab Preparation](#3-lab-preparation)
4. [Lab Context](#4-lab-context)
5. [Learning Objectives](#5-learning-objectives)
6. [Exercise 1: Workspace Initialization and Analysis](#exercise-1-workspace-initialization-and-analysis)
7. [Exercise 2: Application Inventory Generation and Coding Standards](#exercise-2-application-inventory-generation-and-coding-standards)
8. [Exercise 3: Architecture Diagram Creation](#exercise-3-architecture-diagram-creation)
9. [Exercise 4: BANKDATA Program Documentation](#exercise-4-bankdata-program-documentation)
10. [Exercise 5: Business Rules and Code Generation](#exercise-5-business-rules-and-code-generation)
11. [Exercise 6: Change Impact Analysis](#exercise-6-change-impact-analysis)
12. [Exercise 7: User Journey Documentation](#exercise-7-user-journey-documentation)
13. [Exercise 8: Email Search Implementation](#exercise-8-email-search-implementation)
14. [Conclusion](#6-conclusion)

---

## 1. Introduction

[↩️](#-table-of-contents)

### What is IBM Bob Premium Package for Z?

**IBM Bob Premium Package for Z** is an AI assistant specialized in analyzing, documenting, and modernizing IBM Z mainframe applications. It combines:

- 🧠 **Advanced Artificial Intelligence** to understand COBOL, PL/I, Assembler, JCL, and REXX code
- 📊 **Automatic analysis** of application structure and dependencies
- 📝 **Documentation generation**, both technical and functional
- 🔍 **Impact analysis** for evolutions and modifications
- 🎯 **Recommendations** for modernization and optimization

### Why This Lab?

This lab will allow you to concretely discover how Bob Premium Package for Z can:

1. **Accelerate understanding** of complex mainframe applications
2. **Automate documentation** technical and functional
3. **Facilitate impact analysis** before modifications
4. **Improve quality** of project documentation
5. **Reduce time** for onboarding new developers

---

## 2. IBM Bob Premium Package for Z Modes

[↩️](#-table-of-contents)

Bob Premium for Z offers several specialized modes for different types of tasks.

> **Note on mode types:** IBM Bob ships with three **built-in modes** available to all users — **Agent**, **Ask**, and **Plan**. The **Z Code** and **Z Architect** modes are **custom modes added by the IBM Bob Premium Package for Z**. They appear alongside the built-in modes once the package is activated.

### 🧰 Z Code *(Premium custom mode)*

**Specialty:** Mainframe code analysis and documentation

- Analysis of COBOL, PL/I, JCL, Assembler, REXX programs
- Business rules extraction
- Application inventory generation
- Data dictionary creation
- Detailed technical documentation

**When to use it:**

- Source code analysis
- Business logic understanding
- Program documentation
- Pattern extraction

### 📐 Z Architect *(Premium custom mode)*

**Specialty:** Mainframe application architecture and design

- Architecture diagram creation
- Change impact analysis
- Feasibility assessment
- Evolution planning
- Dependency analysis
- Architectural documentation

**When to use it:**

- Architecture design
- Impact analysis
- Feasibility studies
- Project planning
- Architecture reviews

### ❓ Ask *(built-in mode)*

**Specialty:** Explanations and user documentation

- Non-technical documentation
- User guides
- Educational explanations
- Question answering
- Training

**When to use it:**

- User documentation
- Training guides
- Simplified explanations
- FAQ and support

### 💻 Agent *(built-in mode)*

**Specialty:** Executing commands, writing and modifying code

- Running system and Git commands
- Code writing, refactoring, and bug fixes
- Feature implementation
- File creation and manipulation

**When to use it:**

- Workspace and environment setup (Git, file operations)
- New feature development
- Code modifications and corrections

### 📝 Plan *(built-in mode)*

**Specialty:** Planning and strategy

- Project planning
- Strategy definition
- Roadmaps
- Technical specifications

**When to use it:**

- Project planning
- Strategy definition
- Specification creation

> **Switching modes:** Use the **dropdown selector** to the left of the chat input field to switch modes.

---

## 3. Lab Preparation

[↩️](#-table-of-contents)

### 🎯 Objective

Retrieve the CBSA application source code from GitHub and prepare your workspace for the lab.

### ✅ Prerequisites

- IBM Bob Version 2 installed (macOS, Linux, or Windows) — download from https://bob.ibm.com/download
- A subscription with access to Bob Premium Package for Z
- Git and internet access available
- You are able to access www.github.com to retrieve the source code
- The following extensions will be installed automatically in Bob, when you login the first time:

| Extension                     | Tested version (or latest) |
| ----------------------------- | -------------------------- |
| Zowe Explorer                 | 3.5.1                      |
| IBM Z Open Editor             | 6.7.0                      |
| IBM Bob Premium Package for Z | 3.0.21                     |
| Mermaid                       | 2.7.7                      |

- **Bob IDE tested version:** 1.126.0+bob2.1.0

When you start Bob for the first time, it will ask you some questions about the setup.  For
the purpose of the workshop you should responds like this:
1. If asked about a password for the keyring, just press Continue without a password
2. IF asked about storing passwords unencrypted, just press Continue
3. When asked if you want to import settings and extensions from a previous version, just press "Skip For Now". 
4. Use the blue "Login to Bob" button when there are no more popups appearing.
5. If you at some point get a message box requesting you to create a Mermaid account, just cancel it, and it will continue to function as required without an account. 

### 📝 Context

Before starting the analysis, you need to retrieve the CBSA application source code from a public GitHub repository.

1. Create a `CBSA` directory in your home directory.
2. Open Bob IDE, click **File > Open Folder**, and select the `~/CBSA` directory.
   (remember to trust the workspace, to avoid IDE go into the **Restricted** mode)
3. Open a terminal by choosing **Terminal → New Terminal**, then run:

```bash
git clone https://github.com/fbh112/CBSA-base-source.git
```

Once cloned, you will find the following subdirectories under your workspace:

```
CBSA-base-source/
├── README.md
├── bms_src/
├── cobol_copy/
└── cobol_src/
```

### 🎯 You're Ready!

Once your workspace is set up, click the **`+` icon** in the Bob panel header to start a new conversation.

---

## 4. Lab Context

[↩️](#-table-of-contents)

### The Application: CICS Banking Sample Application (CBSA)

You will work on a real banking application that simulates bank teller operations:

**Technical characteristics:**

- **Platform:** IBM z/OS with CICS Transaction Server V5.4+
- **Language:** IBM Enterprise COBOL for z/OS
- **Database:** IBM Db2 V12+
- **Interface:** 3270 terminals with BMS (Basic Mapping Support)
- **Architecture:** Transaction processing with online and batch programs

**Business functionalities:**

- Customer creation and management
- Bank account creation and management
- Account inquiry
- Transfers between accounts
- Account debit/credit
- External credit agency simulation

**Complexity:**

- 29 COBOL programs
- 37 copybooks
- 9 BMS screens
- 3 Db2 tables
- Multi-tier architecture (presentation, business logic, data)

### Initial Situation

You have just joined the maintenance team for this application. You have the source code but:

- ❌ No up-to-date technical documentation
- ❌ No program inventory
- ❌ Undocumented architecture
- ❌ Business rules not formalized
- ❌ Dependencies between programs unknown
- ❌ Impact of modifications difficult to assess

**Your mission:** Use IBM Bob Premium Package for Z to analyze and document this application in a few hours instead of several weeks.

### Preliminary Remarks

- Bob relies on AI agentics, which by definition is not deterministic. For some exercises, you may therefore notice slight differences between your result and the one described in this lab. And sometimes, you will have to add additional instructions to Bob to get the result you expect. This is, in general, the approach to have with Bob: it always gets to the result, but sometimes you have to take a different path to get there.
- Counts made by AI can be inaccurate. Therefore, calculations should be done by engineering rather than by AI.
- IBM Bob Premium Package for Z relies on existing metadata or metadata it builds in a local database. In the discussion with Bob, it may ask whether to use a centralized metadata repository (e.g., "Do you want to use the Z Understand service for a complete dependency analysis, or analyze only the local workspace?"). **In the context of this lab, we do not use the Z Understand service.**
- In the various exercises to follow, Bob will be able to propose different options in response to a prompt. It is important to choose the right option to obtain the desired result. In any case, you can always go back and test another option.

---

## 5. Learning Objectives

[↩️](#-table-of-contents)

At the end of this lab, you will be able to:

✅ **Initialize** a mainframe workspace with IBM Bob Premium Package for Z
✅ **Automatically generate** a complete application inventory
✅ **Create** visual architecture diagrams and comprehensive call graphs
✅ **Analyze** business rules buried in code
✅ **Assess** the impact of modifications on the application
✅ **Document** user journeys
✅ **Propose** technical evolutions with feasibility analysis

---

## Exercise 1: Workspace Initialization and Analysis

[↩️](#-table-of-contents)

### 🎯 Objective

Initialize the workspace and create the `AGENTS.md` file that will serve as a guide for Bob and developers.
Create metadata from existing programs and resources, and generate the data dictionary.

### 🔧 Bob Mode to Use

**Mode: 🧰 Z Code**

Z Code mode is specialized for analyzing and documenting mainframe applications (COBOL, PL/I, JCL, Assembler, REXX).

### 📝 Context

You have a CBSA project that you are discovering. You need to:

- Understand the project structure
- Identify the languages used
- Locate important files
- Build metadata that will facilitate project analysis and the data dictionary that will enable more relevant documentation.

### ✍️ Your Task

Write your own prompt to ask Bob to initialize and fully analyze the workspace.

**Expected in your prompt:**

- request workspace initialization
- request a global analysis of the project structure
- request the creation of framing artifacts (like `AGENTS.md`)
- specify that you want to quickly understand the application content

### 💬 Bob Prompt

```text
/init and create the local metadata database with the scan_program tool.
```

**Note:** `/init` is a **built-in Bob slash command**. It scans the project and generates persistent context files for Bob: `AGENTS.md` in the workspace root

This file give Bob persistent knowledge of your project across all conversations, so it does not need to rediscover the codebase each time. The `scan_program` part of the prompt is specific to the IBM Bob Premium Package for Z and builds the local metadata database for mainframe analysis.

You can find more tools at https://www.ibm.com/docs/en/bobz/3.0.0?topic=commands-using-tools-tool-groups

### 🔀 Prompt Variants

```text
Initialize this workspace and analyze the complete project structure.
```

```text
Analyze this mainframe repository, identify key components and prepare necessary framing files.
```

### ⚙️ What Bob Does Automatically

Bob will:

- Analyze the COBOL workspace (structure, languages, files)
- Detect present mainframe languages
- Check for data dictionary existence
- Search for coding standards
- Locate technical documentation
- Map COBOL programs to their documentation
- Display detected configuration for confirmation
- Update `AGENTS.md` with non-obvious information

### ✅ Sample Result

`AGENTS.md` file is created under workspace root

### ✍️ Bob Action - generate the data dictionary

To initiate the workflow, click the **Start Workflow** button (the ▶ play icon in the Bob panel toolbar), select **"Generate data dictionary"**, then click **"Start"**.
Bob will ask you to select the program from which the data dictionary will be built: choose `cobol_src/BANKDATA.cbl` (you can only select one program at a time). After successful creation of the data dictionary, it will be opened in an editor, and you can review and optionally review. When you are done, the click on the "I am done editing" button in the Bob chat, and the dictionary will be saved.

### ⚙️ What Bob Does Automatically

Bob will use specific ***Skills*** and ***tools*** from the ***IBM Bob Premium Package for Z***. It will:

- Check for `.bobz/local-settings.json` and database existence
- Scan COBOL programs if necessary
- Extract variables from programs
- Create the `bobz/DD.json` data dictionary
- Update `AGENTS.md` with dictionary location

### ✅ Sample Result

Data dictionary successfully created for the CBSA banking application in file `.bobz/DD.json`

Summary of actions:

- Scan of 29 COBOL programs in the source
- Analysis database created and stored in `.bobz/local-settings.json`
- Variable extraction from BANKDATA program
- Selection of 15 critical business variables
- Expansion and documentation of variables with business context
- Creation of `bobz/DD.json` file with 15 documented entries
- Update of `AGENTS.md` with dictionary location

If `AGENTS.md` is not updated by the workflow, ask Bob to do so:

```text
verify if @AGENTS.md is up to date with data dictionary?
```

### ✍️ Update AGENTS.md — Set the Documentation Output Folder

Now that the workspace is initialized, tell Bob where all generated documentation should be saved. This ensures that every subsequent exercise stores its output consistently under `docs/`.

```text
Update @AGENTS.md to specify that all generated documentation, reports, and diagrams must be saved under the docs/ folder.
```

Bob will update `AGENTS.md` with a convention that it will follow in all future conversations, so you will not need to repeat the instruction in every prompt.

### 🎓 What You Learn

- **The importance of initialization**: The workspace is the starting point for all analyses. Some steps are essential before going further. You need to generate metadata (see `.bobz/local-settings.json`) and the data dictionary (see `bobz/DD.json`) to have quality analyses.
- **Time savings:** Instead of spending 2-3 days manually exploring the code, Bob analyzes everything in a few minutes
- **Standardization:** Conventions are automatically established
- **Living documentation:** The `AGENTS.md` file serves as a reference for the entire team

---

## Exercise 2: Application Inventory Generation and Coding Standards

[↩️](#-table-of-contents)

### Step 1 — 🎯 Objective: Application Inventory

Generate a complete application inventory with all programs, copybooks, BMS screens, Db2 tables and VSAM files.

### 🔧 Bob Mode to Use

**Mode: 🧰 Z Code**

Z Code mode automatically analyzes mainframe application structure and generates detailed inventories.

### 📝 Context

Now that the workspace is initialized, you need a detailed inventory to:

- Know all application components
- Understand program categories
- Identify dependencies
- Have an overview of the architecture

### ✍️ Your Task

Write your own prompt to request a complete CBSA application inventory. Feel free to refine your request with several successive prompts if necessary.

**Expected in your prompt:**

- request a global application inventory
- cover programs, copybooks, screens, and data
- request categorization or role per component
- request an exploitable output in a document

### 💬 Bob Prompt

```text
Generate a complete inventory of the CBSA application, with for each program, their type, role, and dependencies (used copybooks, BMS screens, DB2 tables and files used - with access mode -, queues, and called programs).

```

### 🔀 Prompt Variants

```text
Create a CBSA application inventory document with technical components, their uses, and main flows.
```

```text
Map CBSA components: COBOL programs, copybooks, BMS maps, Db2 tables, and main dependencies.
```

### ⚙️ What Bob Does Automatically

Bob will scan, analyze, and document all application components by creating a structured inventory. It will use the Premium for Z tool ***execute_sql_query*** to access the metadata database to extract source information and generate a structured Markdown document.

### ✅ Sample Result

Created file: `docs/CBSA-inventory.md`

Contains:

- Executive summary with statistics
- Inventory of COBOL programs and their dependencies

### Step 2 — 🎯 Objective: Coding Standards

Discover and document the current patterns to apply them to new code (or assert them to the current code).

### 🔧 Bob Mode to Use

**Mode: 🧰 Z Code**

Z Code mode automatically analyzes mainframe application structure and generates detailed inventories.

### 📝 Context

Now that the workspace is initialized and you have a detailed inventory, you need to find:

- coding practice
- naming conventions
- error handling practice
- …

### 💬 Bob Prompt

```text
/z-coding-standards-skill-builder
```

Reply **No** when Bob proposes you to include "ZCodeScan validation" in the skill, for the sake of simplicity in the lab process.

### ⚙️ What Bob Does Automatically

Bob will scan, analyze, and document all application components. It will use the IBM Bob Premium Package for Z skill tool ***/z-coding-standards-skill-builder*** to analyse the code, variable and paragraphs names, error handling...

At the end of Bob output, you will find examples on how to activate this new skill, for example
"generate code", "review code", "check standards", "refactor code", "write a new program"

### ✅ Sample Result

Created file: `.bob/skills/cbsa-coding-standards/SKILL.md` with more details in `references` sub-directory

Contains:

- patterns
- checklist
- sample code
- ...

### ✅ Testing the new skill

```text
verify @BNK1CAC.cbl is respecting cbsa coding standards
```

Only for some lines of code:
Either select a code block. Right click > IBM Bob > Improve code
Or using the prompt:

```text
Improve the following code from base/cobol_src/BNK1CAC.cbl:239-254
```

Bob use the **Z Context** tool to automatically enrich Bob responses with approved IBM Z documentation to improve knowledge and guidance for COBOL and Assembler (ASM) development tasks.

You can read more about the **zContext** tool [here](https://www.ibm.com/docs/en/bobz/3.0.0?topic=z-integrating-context-tool).

---

## Exercise 3: Architecture Diagram Creation

[↩️](#-table-of-contents)

### Step 1 — 🎯 Objective: Architecture Diagram

Create a visual architecture diagram that clearly communicates the **business-level structure** of the CBSA application — helping a new team member understand how the application is organized without getting lost in technical noise.

### 🔧 Bob Mode to Use

**Mode: 📐 Z Architect**

Z Architect mode specializes in creating architecture diagrams and analyzing application flows.

### 📝 Context

The textual inventory from Exercise 2 is useful, but a visual representation is essential for communicating the architecture to a broader audience — architects, project managers, and new developers alike.

However, a naive diagram of all program dependencies tends to be cluttered: utility programs (e.g. error handlers, date/time routines) and language runtime artifacts appear alongside the core business logic, making the diagram hard to read. The goal here is a **focused, business-relevant diagram** that shows:

- the functional layers of the application
- how business programs interact with screens, databases, and external services
- the major data flows — without utility and runtime noise

### ✍️ Your Task

Write your own prompt to request a visual architecture diagram of the application, scoped to business-relevant components.

**Expected in your prompt:**

- request functional application layers
- request key flows and dependencies between business programs
- request integration of screens, databases, and external services
- explicitly ask to exclude utility programs and language runtime artifacts

### 💬 Bob Prompt

```text
Build an architecture diagram illustrating the CBSA application structure, showing functional layers, business programs, BMS screens, Db2 tables, and external services. Exclude utility programs and language runtime artifacts to keep the diagram focused on business logic.
```

### ✅ Sample Result

Mermaid diagram rendered directly in the newly created file `docs/cbsa-architecture-Diagram.md`, showing 4 clean business layers — free of utility and runtime clutter:

- Presentation (3270 terminals / BMS screens)
- Business logic (CICS programs)
- Data access (Db2 tables)
- External services (credit agencies)

### Step 2 — 🎯 Objective: Call Graph Generation

Generate comprehensive call graphs for all programs in the workspace and the local database, organized by functional topics.

### 🔧 Bob Mode to Use

**Mode: 📐 Z Architect**

Z Architect mode is ideal for mapping inter-program relationships, tracing call chains, and producing structured documentation that shows how programs collaborate across functional domains.

### 📝 Context

The architecture diagram from Step 1 gives a high-level view of the application layers. Call graphs go one level deeper: they show **exactly which program calls which**, enabling developers and architects to:

- trace execution paths end-to-end
- identify shared subroutines and reuse patterns
- understand coupling between functional areas
- onboard new team members onto specific business flows quickly

Organizing the output by **functional topic** (e.g., account management, customer management, payment processing) makes the documentation immediately useful even for very large workspaces.

### ✍️ Your Task

Write your own prompt to request call graph documentation, covering all programs in the workspace and the local database, grouped by business function.

**Expected in your prompt:**

- request call graphs for all programs in the workspace
- include the local database in the analysis scope
- organize the output by functional topics
- produce the result as structured documentation (e.g., a Markdown file)

### 💬 Bob Prompt

```text
Create documentation with comprehensive call graphs of all programs in the workspace and the local database, organized by functional topics.
```

### ✅ Sample Result

Bob produces a Markdown document (e.g., `docs/cbsa-call-graphs.md`) containing:

- One section per functional topic (Account Management, Customer Management, Payment Processing, Utility Services, …)
- For each topic: a Mermaid call graph showing the full call chain between CICS programs, copybooks, and Db2 stored procedures
- Cross-references to database tables accessed within each call chain
- A summary table listing all programs, their callers, and their callees

---

## Exercise 4: BANKDATA Program Documentation

[↩️](#-table-of-contents)

### 🎯 Objective

Generate complete technical documentation for the `BANKDATA` batch program that initializes application data.

### 🔧 Bob Mode to Use

**Mode: 🧰 Z Code**

Z Code mode specializes in detailed analysis and documentation of COBOL programs.

### 📝 Context

`BANKDATA.cbl` is the batch data initialization program. It is critical to understand:

- How data is generated
- Which structures are initialized
- Test data generation rules
- Dependencies with VSAM and DB2

### ✍️ Your Task

Write your own prompt to request complete technical documentation for the `BANKDATA.cbl` program.

**Expected in your prompt:**

- explicitly name the target program
- request to explain or document the program
- you are not required to specify explanation sections, as IBM Bob Premium Package for Z provides a default document structure.

### ✅ Bob Action

Click the **Start Workflow** button (the ▶ play icon in the Bob panel toolbar), select **"Generate program documentation"**, choose `BANKDATA.cbl` as the target file.

> ⚠️ **Known platform difference — file selection in the workflow dialog**
>
> - **macOS**: You can select a single file directly from the workflow file picker dialog.
> - **Windows**: The file picker does not support single-file selection. As a workaround, open `BANKDATA.cbl` in the editor first, then start the workflow — the file will be pre-selected automatically.

### ✅ Sample Result

workflow completed, file is created: `docs/program/.../BANKDATA.md`

IBM Bob Premium Package for Z uses a specialized workflow to generate detailed technical documents from COBOL source code (whole-file explanation). Based on your current mode, Bob will automatically produce either developer documentation or architecture documentation.

### 🎓 What You Learn

- **Automatic documentation**: Bob analyzes code and generates structured documentation
- **Batch understanding**: Batch processing logic with VSAM and DB2
- **Parameterization**: Using PARMs to make programs flexible
- **Data generation**: Techniques for creating coherent test data sets
- **Error handling**: VSAM and DB2 error handling patterns

### 📝 Using the Documentation

This documentation is useful for:

1. **Onboarding**: New developers quickly understand the purpose of program `BANKDATA.cbl`
2. **Maintenance**: Reference when modifying the program
3. **Testing**: Understanding how to generate data sets
4. **Migration**: Documenting behavior before modernization
5. **Audit**: Traceability of initialization logic

---

## Exercise 5: Business Rules and Code Generation

[↩️](#-table-of-contents)

### 🎯 Objective

Add a new input validation rule to `BNK1CAC.cbl` — a customer number must start with `99` — and have Bob update the program documentation to reflect the change, keeping code and docs in sync.

### 🔧 Bob Mode to Use

**Mode: 🧰 Z Code**

Z Code mode excels at analyzing COBOL code, implementing new business rules, and keeping documentation consistent with code changes.

### 📝 Context

`BNK1CAC.cbl` is the create account program. It already validates customer number input in several ways (length, no underscores, numeric). You will add one more rule: the customer number must start with `99`.

> **💡 Tip:** If you want to first explore the existing validation rules before making changes, you can ask Bob:
>
> ```text
> extract the business rules from @BNK1CAC.cbl and save them to a markdown file
> ```
>
> This will produce a `docs/BNK1CAC-business-rules.md` file listing all current input validation rules, which you can use as a reference.

### ✍️ Your Task

Add the new validation rule directly into the program and have Bob update the documentation in one step.

### 💬 Bob Prompt

Open `BNK1CAC.cbl` in the editor.

In the Bob prompt, type:

```text
@BNK1CAC.cbl add an input validation rule to verify that a customer number starts with 99, then update the documentation to reflect this change
```

### ✅ Sample Result

**1 — `BNK1CAC.cbl` is updated** with a new validation block inserted after the existing numeric check:

```cobol
           IF CUSTNOI(1:2) NOT = '99'
              MOVE SPACES TO MESSAGEO
              STRING 'Customer number must start with 99'
                    DELIMITED BY SIZE,
                     ' ' DELIMITED BY SIZE
                 INTO MESSAGEO
              MOVE 'N' TO VALID-DATA-SW
              MOVE -1 TO CUSTNOL
              GO TO ED999
           END-IF.
```

**2 — Documentation is updated** to include the new rule and its associated error message.

If Bob does not update the documentation automatically, prompt it explicitly:

```text
verify if @BNK1CAC-business-rules.md reflects the code change in @BNK1CAC.cbl
```

### 💬 Fix Missing Copybook

In the list of **PROBLEMS** at the bottom of the IDE you should see a message indicating "Unable to find copybook BNK1CAM", right click on the message and select **Fix with Bob**, then accept the prompt.

### ✅ Sample Result

Bob should find the missing copybook should contain BMS field definitions. From the BMS definition, it should recreate the missing copybook:`BNK1CAM.cpy`.

After Bob IDE has recreated `BNK1CAM.cpy`, there still should be errors about the missing DFAID copybook which is part of CICS system libraries.

### 💬 Prompt to Generate the Enhancement Report

```text
Create a markdown formatted report on all of the possible enhancements that could be made on @BNK1CAC.cbl

```

### ✅ Sample Result

The report (in `docs/BNK1CAC-enhancement.md`) should find a bug in move to ABND-TIME, duplicated lines...

---

## Exercise 6: Change Impact Analysis

[↩️](#-table-of-contents)

### 🎯 Objective

Evaluate the impact of changing `SORTCODE` from a fixed value to a variable value to support multiple bank branches.

### 🔧 Bob Mode to Use

**Mode: 📐 Z Architect**

Z Architect mode is ideal for impact analysis, risk assessment, and architectural change planning.

### 📝 Context

The business wants to deploy the application across multiple branches, each identified by its own sort code. Currently, `SORTCODE` is hardcoded throughout the CBSA application. You must evaluate the scope, effort, and risks of making it variable.

What makes this exercise particularly powerful is the role of the **metadata database** built in Exercise 1. Rather than scanning source files line by line, Bob queries the local SQLite metadata database using the `execute_sql_query` tool to instantly identify every program, copybook, and data structure that references `SORTCODE`. This produces:

- **Precise results** — no missed occurrences due to text-search limitations
- **Richer context** — each hit comes with program type, paragraph, and dependency information already resolved
- **Speed** — a query that would take a developer hours of manual grep-and-trace work completes in seconds

Without the metadata, Bob would rely on slower file scanning and could miss indirect references (e.g., a copybook that defines `SORTCODE` and is included by multiple programs). With it, the dependency graph is already known.

### 🔍 Step 1 — Explore How SORTCODE Is Used

Before running a full impact analysis, start by asking Bob to locate and explain how `SORTCODE` is defined and used across the application. This gives you a concrete picture of the scope before committing to a change.

#### 💬 Bob Prompt

```text
How is the "sort code" or "sortcode" variable used in the application?
```

#### ✅ Sample Result

Bob queries the metadata database and returns a summary of every location where `SORTCODE` appears — which programs reference it, in which paragraphs, and whether it originates from a copybook or is defined locally. This confirms the breadth of the change before any analysis begins.

---

### 🎯 Step 2 — Run the Impact Analysis

### ✍️ Your Task

Write your own prompt to request a full impact analysis of the `SORTCODE` change.

**Expected in your prompt:**

- clearly describe the target change
- request the list of impacted components
- request effort and risk estimation
- request a transition or migration plan

### 💬 Bob Prompt

```text
Analyze the impact of changing SORTCODE to support multiple bank branches.
```

### 🔀 Prompt Variants

```text
Evaluate the technical and project consequences of moving to a variable SORTCODE in CBSA.
Provide:
- List of programs to modify
- Estimated development effort
- Identified risks
- Data migration plan
- Estimated cost
```

```text
Perform a complete impact analysis to transform the fixed SORTCODE into multi-branch data, with risks, effort, and migration.
```

### ✅ Sample Result

Bob triggers the built-in **impact-analysis** skill and uses `execute_sql_query` to interrogate the metadata database. It may ask clarifying questions to help you choose the best design strategy — select the option that best fits your requirements.

Because the metadata already captures cross-program dependencies, Bob can immediately report:

- which programs directly reference `SORTCODE`
- which copybooks define or propagate it
- the downstream programs affected through those copybooks

**File created:** `docs/CBSA-archi-impact***.md`

> **💡 Key takeaway:** The quality and completeness of this impact analysis depend directly on the metadata built during workspace initialization. The richer the metadata, the faster and more accurate Bob's dependency traversal becomes — turning what would normally be a multi-day manual audit into a few minutes of conversation.

---

## Exercise 7: User Journey Documentation

[↩️](#-table-of-contents)

### 🎯 Objective

Create user journey documentation for account consultation, intended for tellers.

### 🔧 Bob Mode to Use

**Mode: ❓ Ask**

Ask mode is ideal for creating non-technical documentation for end users.

### 📝 Context

Tellers need a simple, non-technical guide with concrete examples.

### ✍️ Your Task

Write your own prompt to request business documentation of the user journey for account consultation.

**Expected in your prompt:**

- specify target audience (tellers)
- request non-technical language
- request complete path from main menu
- request screen examples and practical tips

### 💬 Bob Prompt

```text
Create user journey documentation for consulting
a customer's accounts in the CBSA application.

The documentation must:
- Be intended for tellers (non-technical)
- Show the path from the main menu
- Include screen examples
- Provide practical tips
```

### 🔀 Prompt Variants

```text
Write a simple business guide explaining how a teller consults a customer's accounts in CBSA.
```

```text
Document step-by-step the customer account consultation journey in CBSA, with a pedagogical tone and visual examples.
```

### ✅ Sample Result

**File created:** `docs/teller-guide-Account consultation.md`

Document containing:

- Illustrated step-by-step guide
- 3270 screen examples
- Practical tips
- Troubleshooting guide
- Real use cases

If it does not create a new file , you can ask Bob to

```
Write the result into a markdown file under /docs folder
```

---

## Exercise 8: Email Search Implementation

[↩️](#-table-of-contents)

### 🎯 Objective

Design and implement a new feature allowing customer identification by email address.

### 🔧 Bob Mode to Use

**Modes: 📐 Z Architect → 🧰 Z Code**

- 📐 Z Architect: To define and plan the complete solution for Z/OS
- 🧰 Z Code: To generate COBOL code for the new feature

### 📝 Context

The business wants to modernize the application by allowing customers to be identified by email rather than customer number. This evolution requires:

- Adding an email field to data structures
- Creating an alternate VSAM index for search
- Developing a new search program
- Modifying existing programs

You can first verify current search modes to ensure the new feature is consistent with existing functionality. The following prompt allows this verification:

```text
what criteria can be used to search for a customer in CBSA?
```

Bob will tell you at the end that "There is exactly one search criterion: the customer number."

### Part A: Implementation Planning

#### 🔧 Mode: 📐 Z Architect

#### 💬 Bob Prompt

```text
What is the plan to implement customer search by email?
```

#### ✅ Sample Result

Bob will trigger a built-in skill **implementation-planning** to produce a phased plan.
Bob creates `docs/CBSA-plan-email-search.md` or similar.

#### 🎓 What You Learn

- **Structured planning**: Breakdown into logical sprints
- **Realistic estimation**: Effort and cost based on experience
- **Risk management**: Identification and mitigation
- **Iterative approach**: Progressive value delivery

---

### Part B: Data Structure Modification

#### 🔧 Mode: 🧰 Z Code

Z Code mode excels at pattern analysis and extracting business rules embedded in COBOL code.

#### 💬 Bob Prompt

```text
According to the implementation plan, update the CUSTOMER data structures
```

#### ✅ Sample Result

Bob creates/modifies files in the `base/cobol_copy/` directory, for example:

1. `CUSTOMER.cpy` (main structure)
2. `CRECUST.cpy` (creation COMMAREA)
3. `INQCUST.cpy` (inquiry COMMAREA)
4. `UPDCUST.cpy` (update COMMAREA)
5. `INQEMAIL.cpy` (new — email search COMMAREA) — note: the name could be different during your test

**Design Decisions**:

- **Email length**: 100 characters (covers 99% of real emails per RFC 5321)
- **Verification flag**: Allows tracking if email has been verified
- **Storage**: Emails in uppercase for consistent search
- **Record length**: Maintained at 399 bytes with FILLER adjustment

#### 🎓 What You Learn

- **Structure consistency**: All copybooks updated together
- **Backward compatibility**: Record length preserved
- **COBOL standards**: Respect for naming conventions
- **Speed**: copybook files modified in minutes vs several hours manually

---

### Part C: Search Program Development

#### 🔧 Mode: 🧰 Z Code

Z Code mode excels at generating COBOL code for Z/OS.

#### 💬 Bob Prompt

```text
Create the INQEMAIL program according to the implementation plan
```

> **Note:** The program name could be different during your test — prompt based on your implementation proposal result.

#### ✅ Sample Result

Bob develops **`base/cobol_src/INQEMAIL.cbl`**:

#### 🎓 What You Learn

- **COBOL code generation**: Bob creates production-ready code
- **CICS programming**: Proper use of CICS commands
- **Error handling**: Complete error management
- **Code quality**: Structured, commented, maintainable code

---

### Part D: Program Syntax Verification

#### 🔧 Mode: 🧰 Z Code

Z Code mode excels at generating COBOL code for Z/OS.

#### 💬 Bob Prompt

```text
Verify the syntax of the program @INQEMAIL.cbl
```

#### ✅ Sample Result

Bob performs a **complete syntax verification** of the generated program, and gives suggestions to fix it.

---

## 6. Conclusion

[↩️](#-table-of-contents)

### 🎉 Congratulations!

You have completed the IBM Bob Premium Package for Z lab. In a few hours, you have:

- Initialized and analyzed a complex mainframe workspace
- Generated an exhaustive application inventory
- Created a professional architecture diagram and comprehensive call graphs
- Documented the BANKDATA batch program
- Analyzed occurrences of business rules
- Evaluated the impact of a major change
- Documented a complete user journey
- Proposed an evolution with implementation guide

### 🔧 Mode Usage Summary

During this lab, you used different Bob modes according to needs:


| Exercise                        | Mode Used　　　　　　　　　| Reason for Choice                                                |
| ---------------------------------| ----------------------------| ------------------------------------------------------------------|
| 1. Initialization               | 🧰 Z Code　　　　　　　　　| Mainframe code analysis and technical documentation creation     |
| 2. Inventory & Coding Standards | 🧰 Z Code　　　　　　　　　| Exhaustive scan and analysis of COBOL components                 |
| 3. Architecture & Call Graphs   | 📐 Z Architect　　　　　　 | Architecture diagram + call graph generation by functional topic |
| 4. BANKDATA Documentation       | 🧰 Z Code　　　　　　　　　| Detailed technical documentation of a batch program              |
| 5. Business Rules & Code Gen    | 🧰 Z Code　　　　　　　　　| Pattern extraction and inline code generation                    |
| 6. Impact Analysis              | 📐 Z Architect　　　　　　 | Impact assessment and change planning                            |
| 7. User Journey                 | ❓ Ask　　　　　　　　　　　| Non-technical documentation for end users                        |
| 8. Email Search Implementation  | 📐 Z Architect → 🧰 Z Code | Planning (Z Architect) + COBOL code generation (Z Code)          |

**Key principle:** Choosing the right mode according to task nature maximizes efficiency and result quality.

### 📚 Created Resources

During this lab, you generated:


| Document                               | Lines           | Value                           |
| -------------------------------------- | --------------- | ------------------------------- |
| AGENTS.md                              | 122             | Reference guide                 |
| CBSA-INVENTORY.md                      | 850+            | Complete inventory              |
| cbsa-architecture-Diagram.md (Mermaid) | -               | Visual architecture diagram     |
| cbsa-call-graphs.md                    | -               | Call graphs by functional topic |
| BANKDATA-docu-technique.md             | 450+            | Batch program documentation     |
| BNK1CAC-business-rules.md              | ~100            | Business rules (optional)       |
| CBSA-SORTCODE-CHANGE-IMPACT.md         | 782             | Impact analysis                 |
| CBSA-USER-JOURNEY-CUSTOMER-ACCOUNTS.md | 485             | User guide                      |
| CBSA-EMAIL-ENHANCEMENT-GUIDE.md        | 1247            | Evolution guide                 |
| **TOTAL**                              | **4618+ lines** | **Complete documentation**      |

### 📊 Gains Summary


| Task                    | Without Bob     | With Bob       | Gain      |
| ----------------------- | --------------- | -------------- | --------- |
| Initialization          | 2-3 days        | 3 minutes      | 99.8%     |
| Inventory               | 1-2 weeks       | 10 minutes     | 99.5%     |
| Architecture diagram    | 2-3 days        | 5 minutes      | 99.7%     |
| Call graphs             | 1-2 days        | 5 minutes      | 99.7%     |
| BANKDATA Documentation  | 1-2 days        | 5 minutes      | 99.7%     |
| Business rules          | 1 week          | 10 minutes     | 99.6%     |
| Impact analysis         | 2 weeks         | 15 minutes     | 99.7%     |
| User journey            | 3-4 days        | 10 minutes     | 99.6%     |
| Feasibility study       | 1-2 weeks       | 15 minutes     | 99.7%     |
| Automatic documentation | 2-3 weeks       | 3 minutes      | 99.8%     |
| **TOTAL**               | **11-17 weeks** | **76 minutes** | **99.6%** |

### 💼 Enterprise Use Cases

IBM Bob Premium Package for Z is particularly useful for:

- **Onboarding**: Accelerate new developer ramp-up
- **Maintenance**: Quickly understand legacy code
- **Modernization**: Analyze evolution feasibility
- **Documentation**: Maintain up-to-date documentation
- **Audit**: Prepare code and architecture reviews
- **Training**: Create educational materials

**Thank you for participating in this lab!**

**Lab Version:** 2.0
**Creation date:** 2026-09
**Author:** IBM Bob Premium Package for Z Team

---
