# Lab: IBM Bob Shell for Automation
## Using Bob Shell to Assist Automation task from the Terminal

- **Estimated Duration:** 1-2 hours  
- **Level:** Intermediate  
- **Prerequisites:** Basic knowledge of Linux and familiarity with IBM Bob IDE

---

## 📋 Table of Contents

1. [Introduction](#1-introduction)
2. [What is Bob Shell?](#2-what-is-bob-shell)
3. [Lab Preparation](#3-lab-preparation)
4. [Lab Context](#4-lab-context)
5. [Learning Objectives](#5-learning-objectives)
6. [Exercise 1: Starting Bob Shell](#exercise-1-starting-bob-shell)
7. [Exercise 2: Interactive Session — Exploring a Mainframe Project](#exercise-2-interactive-session--exploring-a-mainframe-project)
8. [Exercise 3: Non-Interactive Sessions — Automating Tasks](#exercise-3-non-interactive-sessions--automating-tasks)
9. [Conclusion](#6-conclusion)

---

## 1. Introduction
[↩️](#-table-of-contents)

### What is IBM Bob Shell?

**IBM Bob Shell** brings IBM Bob's AI capabilities directly to your command line. As a terminal-based interface, it provides AI-assistance for command-line tasks, script automation, and terminal-based workflows — delivering the same context awareness and reasoning-focused approach from IBM Bob, but optimized for shell environments and automated processes.

Key things Bob Shell can do:

- 🤖 **Automate scripts** — Generate and optimize shell scripts for complex automation tasks
- ⚙️ **Execute commands** — Run terminal commands with AI-powered assistance and validation
- 📄 **Generate documentation** — Create comprehensive documentation for scripts and workflows
- 🔍 **Troubleshoot issues** — Debug command failures and resolve terminal-based problems
- 📊 **Analyze logs** — Parse and analyze log files to identify issues and patterns
- 🏗️ **Scaffold projects** — Initialize new projects and generate boilerplate code from the terminal

### Why This Lab?

For mainframe developers, Bob Shell offers a powerful complement to Bob IDE:

1. **Work where you are** — Use Bob's AI assistance directly in a terminal session, without opening the IDE
2. **Automate repetitive operations** — Script complex, multi-step workflows once and reuse them
3. **Integrate into CI/CD pipelines** — Drive AI-assisted code generation and analysis in automated processes
---

## 2. What is Bob Shell?
[↩️](#-table-of-contents)

Bob Shell is a **standalone, terminal-based AI assistant** installed as a CLI tool (`bob`) on your workstation. It is separate from — but complementary to — Bob IDE.

### 🖥️ Flexible Ways to Run Bob Shell

| Mode                        | Description                                           | Best For                                                  |
| -----------------------------| -------------------------------------------------------| -----------------------------------------------------------|
| **Interactive session**     | Conversational session in your terminal  (`bob chat`) | Exploratory tasks, problem-solving, multi-step workflows  |
| **Non-interactive session** | Single prompt, single output (`bob run "..."`)         | Automation, scripting, CI/CD pipelines                    |
| **Editor terminal support** | Run Bob Shell inside the terminal tab of Bob IDE      | Seamless IDE + Shell integration with workspace awareness |

> **Tip:** Use interactive sessions for exploratory tasks and problem-solving. Switch to non-interactive sessions when you need to automate repetitive workflows or integrate Bob into scripts.

### 🧭 Bob Shell Modes

Bob Shell includes three purpose-built modes, each optimized for different terminal tasks:

| Mode         | Purpose                                                   |
| --------------| -----------------------------------------------------------|
| **Code**     | Generate, modify, and refactor code from the command line |
| **Ask**      | Get answers about your codebase and development questions |
| **Plan**     | Design and plan implementations before running them       |

Switch between modes by typing `/mode` in the interactive session and selecting from the list, or type `/` to see all available commands.

> **⚠️ Note:** Bob Shell for Z does not currently provide feature parity with IBM Bob Premium Package for Z in the IDE. Capabilities that are available in the IDE, such as support for different operational modes (for example, Z context mode), are not currently supported in Bob Shell for Z.

### 🔐 Authentication

Bob Shell uses the same login entry point as all Bob clients: `bob.ibm.com/login`.

| Method | Use Case |
|---|---|
| **SSO / IBMid** | Interactive sessions — requires browser access |
| **API key** | Automation, CI/CD pipelines, non-interactive environments |

---

## 3. Lab Preparation
[↩️](#-table-of-contents)

### 🎯 Objective

Install Bob Shell on your workstation, authenticate, and verify that it can read your project workspace.

### ✅ Prerequisites

- Bob Shell installed (tested with v2.0.2)
- A subscription with access to IBM Bob (IBMid required)
- Access to the CBSA project workspace used in Lab 1 (or any local COBOL project)


### ✍️ Your Task — Installing Bob Shell

You can install Bob Shell using an installation script, or from your package manager. 

**Option A — Installation script (recommended)**

Select your operating system and run the corresponding command:
refer to doc https://www.ibm.com/docs/en/bobz/3.0.0?topic=z-installing-using-bob-shell


**macOS / Linux**
```bash
curl -fsSL https://bob.ibm.com/download/bobshell.sh | bash
```

**Windows (PowerShell)**
```powershell
powershell -ep Bypass 'irm -Uri "https://bob.ibm.com/download/bobshell.ps1" | iex'
```

**Option B — Package manager (from downloaded package)**

Download the package from https://bob.ibm.com/releases?bob=shell, then:

```bash
# npm (macOS/Linux/Windows)
npm install --reg=https://registry.npmjs.org/ -g "<file-path>"
```

**Verify the installation:**

```bash
which bob
bob --version
```

If `command not found: bob`, check that your shell's `PATH` includes the Bob Shell installation directory.


### 🎯 You're Ready!

Once Bob Shell is installed, open a terminal, navigate to your project directory, and proceed to Exercise 1.

---

## 4. Lab Context
[↩️](#-table-of-contents)

This lab uses the CBSA (CICS Banking Sample Application) project introduced in Lab 1. Bob Shell will be used to:

- Explore and analyze the COBOL source files locally from the terminal
- Generate documentation and code in non-interactive scripts

If you have not yet set up the CBSA workspace and clone the source repo, refer to **Lab 1 — Section 3 (Lab Preparation)** for instructions.

---

## 5. Learning Objectives
[↩️](#-table-of-contents)

By the end of this lab, you will be able to:

- ✅ Install Bob Shell and start an interactive session from the terminal
- ✅ Navigate modes and use slash commands in Bob Shell
- ✅ Analyze COBOL source files and generate code directly from the terminal
- ✅ Run non-interactive Bob Shell commands for automation and scripting

---

## Exercise 1: Starting Bob Shell 
[↩️](#-table-of-contents)

### 🎯 Objective

Start your first Bob Shell interactive session from the terminal and verify that Bob can access the CBSA project workspace.

### 🔧 Bob Shell Mode to Use

**Mode: Agent** (default when starting)


Bob Shell uses **trusted folders** to control which projects it can access. When you run Bob Shell from a project directory for the first time, a trust dialog appears:

- **Trust folder** — Grant full trust to the current directory
- **Trust parent folder** — Trust the parent directory and all subdirectories
- **Don't trust** — Run in restricted safe mode (project settings and MCP servers are disabled)

> ⚠️ **Remember to trust your folder** to avoid running in restricted safe mode.

When you run Bob Shell for the first time, you are prompted to log in with your IBMid and accept the license agreement. Once authenticated, Bob Shell is ready to use.

### ✍️ Your Task

Start Bob Shell in the CBSA workspace and ask it to give you a high-level overview of the project.

Open a terminal window, and type
```bash
# Navigate to your CBSA workspace root
cd ~/CBSA/CBSA-base-source

# Start an interactive session
bob chat 
```


### 🧭 Navigating Bob Shell — Explore Before You Start

Before running any task, take a moment to explore what Bob Shell offers. All built-in commands are accessible by typing `/` in the prompt.

**1. List all available commands**

```text
/
```

This opens the slash command menu. Browse available commands, modes, and any custom commands loaded from `.bob/commands/`. Press `Escape` to dismiss without selecting.

**2. Get help**

```text
/help
```

Displays a summary of Bob Shell usage, key shortcuts, and available commands.


**3. Switch mode**

```text
/mode
```

This displays an interactive list of available modes. Use the arrow keys to highlight the target mode and press `Enter` to select it:

| Mode  | Purpose                             |
| -------| -------------------------------------|
| Agent | Generate and modify code            |
| Ask   | Read-only Q&A about your codebase   |
| Plan  | Reason and plan before implementing |
**4. Check your current model and settings**

```text
/settings
```
Shows the active configuration: current model, approval mode, and key settings from `.bob/settings.json`.

**5. Check your current team**

```text
/team
```
Shows the current team that you are connected to


---


## Exercise 2: Interactive Session — Exploring a Mainframe Project
[↩️](#-table-of-contents)

### 🎯 Objective

Use Bob Shell in Ask mode to progressively explore the CBSA project: start with a high-level overview of the workspace, then drill down into a specific COBOL program, and finally trace its dependencies across the codebase.

### 🔧 Bob Shell Mode to Use

**Mode: Ask** — read-only Q&A; Bob will not attempt to modify any files.

Type `/mode` and select **Ask** from the list.

### 📝 Context

**Ask** mode is the right choice when you want to understand code without risking any changes. It is optimized for information retrieval and can cross-reference multiple files to answer dependency questions.

You will explore the CBSA project in four progressive steps, each building on the previous one.

---

### Step 0 — Initialize the Workspace

#### 🎯 Goal

Before querying the project, initialize Bob Shell's understanding of the current workspace so that subsequent prompts have full context about the files and structure present.

#### 💬 Bob Shell Command

```text
/init
```

#### ⚙️ What Bob Shell Does

Bob Shell will:
- Scan the current workspace directory
- Index the files and directory structure into its working context
- Prepare a workspace summary that subsequent prompts can reference

#### ✅ Sample Result

Bob Shell confirms that the workspace has been initialized and displays a brief summary of the files found (e.g., number of COBOL source files, copybooks, JCL members detected).

---

### Step 1 — Get a High-Level Project Overview

#### 🎯 Goal

Before looking at individual programs, understand what the CBSA project contains: its structure, languages, and key files.

#### 💬 Bob Shell Prompt

```text
Give me a high-level overview of this project: what languages are used, what are the key directories, and what does this application do?
```

#### ⚙️ What Bob Shell Does

Bob Shell will:
- Scan the workspace directory structure
- Detect languages present (COBOL, BMS, JCL, copybooks)
- Summarize the application purpose and component layout

#### ✅ Sample Result

Bob Shell outputs a structured summary: languages detected (COBOL, BMS, JCL), directory layout (`cobol_src/`, `cobol_copy/`, `bms_src/`), and a brief description of the CBSA banking application's function.

Bob created file `AGENTS.md` in the root folder and a few mode specific  `AGENTS.md` files for each mode 
```
.bob/rules-agent/AGENTS.md
.bob/rules-ask/AGENTS.md
.bob/rules-plan/AGENTS.md
```

---

### Step 2 — Understand a Specific Program

#### 🎯 Goal

Drill down into the `BANKDATA.cbl` batch program — understand its purpose, structure, and the data it defines.

#### 💬 Bob Shell Prompt

```text
@cobol_src/BANKDATA.cbl — explain this program: what is its purpose, what data structures does it define, and what are the key paragraphs in the PROCEDURE DIVISION?
```

#### 🔀 Prompt Variants

```text
What is the role of the WORKING-STORAGE SECTION in @cobol_src/BANKDATA.cbl?
```

```text
List all the paragraphs in @cobol_src/BANKDATA.cbl and describe what each one does.
```

#### ⚙️ What Bob Shell Does

Bob Shell will:
- Read and parse the COBOL source file
- Identify divisions, sections, data definitions, and paragraph names
- Provide a natural language explanation of the program's logic

#### ✅ Sample Result

Bob Shell explains BANKDATA's role as a batch data loader, describes its key WORKING-STORAGE data structures (account and customer records), and lists the paragraphs in the PROCEDURE DIVISION with a one-line description of each.

---

### Step 3 — Trace Dependencies Across the Project

#### 🎯 Goal

Understand how BANKDATA relates to the rest of the project: which copybooks it uses, and which other programs depend on those same copybooks.

#### 💬 Bob Shell Prompt

```text
Which copybooks does @cobol_src/BANKDATA.cbl include, and which other programs in cobol_src/ also use those same copybooks?
```

#### 🔀 Prompt Variants

```text
List all the copybooks included in the COBOL programs under cobol_src/ and show me which programs share them.
```

```text
If I change the data layout in a copybook used by BANKDATA.cbl, which other programs would be affected?
```

#### ⚙️ What Bob Shell Does

Bob Shell will:
- Read the COPY statements in `BANKDATA.cbl`
- Scan the other programs in `cobol_src/` for matching COPY statements
- Build a cross-reference showing shared copybook dependencies

#### ✅ Sample Result

Bob Shell lists the copybooks used by BANKDATA (e.g., `CUSTOMER.cpy`, `SORTCODE.cpy`) and identifies the other programs that include each one — giving a clear picture of the change impact boundary.

### Step 4 — Switch to Plan mode and design a change:

Type `/mode`, select **Plan** from the list, then enter your prompt:
```text
I need to add a new field EMAIL-ADDRESS (PIC X(100)) to the customer record in @CUSTOMER.cpy. What is the safest way to do this without breaking existing programs?
```
Bob will ask you for Design Confirmation Needed,  select which one is best as your strategy.

### ✅ Sample Result

A structured plan document is produced in Plan mode, and saved in current directory `add-email-address-plan.md`


### 🎓 What You Learn

- **`/init`** seeds Bob Shell with full workspace context before any queries — always run it first in a new session
- **Ask mode** is the safe, read-only mode for code exploration — always start here before making changes
- The **`@filename`** context mention pins a specific file into the conversation so Bob focuses on it
- Bob Shell can **cross-reference multiple files** to trace copybook dependencies 
- Building understanding in progressive steps (init → overview → program → dependencies) is the most efficient way to onboard onto an unfamiliar codebase
- Choosing the right mode for the right task improves output quality
- Typing `/mode` and selecting **Plan** prevents premature edits and forces Bob to reason first

Entry Ctrl-C twice to exit the interactive session, before move to next exercise. 

---

## Exercise 3: Non-Interactive Sessions — Automating Tasks
[↩️](#-table-of-contents)

### 🎯 Objective

Use Bob Shell's non-interactive mode (`bob run "..."`) to automate a documentation generation task from the command line, suitable for use in a CI/CD script.

### 🔧 Bob Shell Mode to Use

**Non-interactive session** — invoked with `bob run "..."` from any shell script.


### 📝 Context

Non-interactive sessions allow Bob Shell to be invoked programmatically. This is ideal for:

- Integrating Bob Shell into automation scripts
- Processing multiple files with a single command
- Getting quick insights without starting an interactive session
- Generating documentation from code in a pipeline

> **Note:** Before the first non-interactive use, you need to setup the API key
>Follow this instruct to create a Bob API key and then run
>https://bob.ibm.com/docs/ide/account/api-keys#api-key-types
> 
>`
>export BOB_API_KEY="your-api-key"
>`

### ✍️ Your Task

Write a shell script that uses Bob Shell to generate a one-paragraph summary for each COBOL program in `cobol_src/`, writing the output to a `docs/` directory.

### 💬 Bob Shell Commands

**Option1: Single-file non-interactive example:**

```bash
bob run "Write a one-paragraph technical summary of @cobol_src/BANKDATA.cbl. Output only the summary text, no markdown formatting."
```

**Option2: Batch documentation script (save as `gen-docs.sh`):**

```bash
#!/bin/bash
mkdir -p docs

for cbl in cobol_src/*.cbl; do
  program=$(basename "$cbl" .cbl)
  echo "Documenting $program..."
  bob run "Write a one-paragraph technical summary of @${cbl}. Output only the summary text." \
    > "docs/${program}-summary.txt"
done

echo "Documentation complete. Output in docs/"
```

**Run the script:**

```bash
chmod +x gen-docs.sh
./gen-docs.sh
```

### ✅ Sample Result

A `docs/` folder is populated with `.txt` summary files, one per COBOL program. Each file contains a summary of technical description of the program's purpose, inputs, and outputs.


**Option3: Pipe a multi-line prompt from a file:**

```bash
echo "list current file structuere" >> prompt.txt
cat prompt.txt | bob
```

### ✅ Sample Result
The current file structure of the workspace will be displayed 


### ⚙️ What Bob Shell Does Automatically
- Opens a session scoped to the current directory
- Reads the referenced file(s)
- Outputs the AI response to stdout, which the script captures to file


### 🎓 What You Learn
- Non-interactive mode turns Bob Shell into a scriptable AI tool
- The `bob run` pattern can be embedded in CI/CD pipelines (GitHub Actions, Jenkins, etc.) for automated code documentation

---

## 6. Conclusion
[↩️](#-table-of-contents)

### 🎉 Congratulations!

You have completed the IBM Bob Shell for Z Development lab. In a couple of hours, you have:

- Installed Bob Shell and started your first interactive terminal session
- Used Ask, Plan, Agent modes for different development tasks
- Navigated Bob Shell's slash command menu and mode switching
- Analyzed and document COBOL code entirely from the terminal
- Automated documentation generation with non-interactive `bob run` cmd


### 💼 Enterprise Use Cases

IBM Bob Shell is particularly valuable for:

- **CI/CD pipelines**: Automated code reviews, documentation generation, and quality checks on every commit
- **Onboarding**: Allow new developers to explore and understand legacy COBOL codebases from the terminal
- **Batch modernization analysis**: Script Bob Shell to analyze dozens of programs at once and produce consistent documentation

**Thank you for participating in this lab!**

**Lab Version:** 1.0  
**Creation date:** 2026-09  
**Author:** IBM Bob Premium Package for Z Team

---
