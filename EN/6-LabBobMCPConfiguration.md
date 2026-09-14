# Lab: Configuring MCP Servers in IBM Bob IDE
## Connecting IBM Bob to Mainframe via the Model Context Protocol

- **Estimated Duration:** 1–1.5 hours  
- **Level:** Intermediate  
- **Prerequisites:** Basic knowledge of z/OS and familiarity with IBM Bob IDE, JSON configuration files

> 📖 **Reference:** [IBM Z Open Editor — Advanced Agent Mode & IBM Bob™ Premium Package for Z](https://ibm.github.io/zopeneditor-about/Docs/advanced_agent_mode.html#ibm-bobtm-premium-package-for-z)

---

## 📋 Table of Contents

1. [Introduction](#1-introduction)
2. [MCP Concepts Overview](#2-mcp-concepts-overview)
3. [Lab Preparation](#3-lab-preparation)
4. [Lab Context](#4-lab-context)
5. [Learning Objectives](#5-learning-objectives)
6. [Exercise 1: Understanding the Z Open Editor MCP Server](#exercise-1-understanding-the-z-open-editor-mcp-server)
7. [Exercise 2: Registering the Z Open Editor MCP Server in Bob](#exercise-2-registering-the-z-open-editor-mcp-server-in-bob)
8. [Exercise 3: Verifying MCP Tool Discovery](#exercise-3-verifying-mcp-tool-discovery)
9. [Exercise 4: Managing MCP Tools — Enable, Disable, and Auto-Approve](#exercise-4-managing-mcp-tools--enable-disable-and-auto-approve)
10. [Exercise 5: Using MCP Tools in Agent Mode](#exercise-5-using-mcp-tools-in-agent-mode)
11. [Conclusion](#6-conclusion)

---

## 1. Introduction
[↩️](#-table-of-contents)

### What is MCP and Why Does It Matter for Mainframe Developers?

**Model Context Protocol (MCP)** is an open standard that allows AI agents like IBM Bob to communicate with external tools and services in a structured, extensible way. MCP acts as a bridge between Bob's AI reasoning engine and external capabilities — enabling Bob to discover, invoke, and integrate tools far beyond its built-in feature set.

For mainframe developers using **IBM Bob Premium Package for Z**, MCP is the mechanism through which Bob gains access to the rich set of mainframe operations provided by IBM Z Open Editor. When the Z Open Editor MCP server is connected to Bob, Bob can:

- 🖥️ **List datasets and members** on z/OS using natural language
- 📤 **Upload and download** files and PDS members
- ⚙️ **Issue TSO and USS commands** without a 3270 terminal
- 🔄 **Submit JCL jobs** and retrieve spool output
- 🔍 **Search and analyze** COBOL and PL/I programs
- 🏗️ **Trigger user builds** and inspect DBB build results

Without MCP, Bob can only work with local workspace. With MCP, Bob becomes a full mainframe AI agent.

### Why This Lab?

This lab teaches you:

1. **How MCP works** — the architecture, configuration files, and transport types
2. **How to register the Z Open Editor MCP server** in your Bob IDE
3. **How to control which tools Bob can use** — enabling, disabling, and auto-approving individual MCP tools
4. **How to share MCP configuration** with your team using project-level config files
5. **How to invoke MCP tools** through natural language prompts in Agent mode

---

## 2. MCP Concepts Overview
[↩️](#-table-of-contents)

### 🏗️ Architecture

MCP uses a client-server architecture:

```
┌─────────────────────────────────┐
│         IBM Bob IDE             │
│   (MCP Client / AI Host)        │
│                                 │
│  Agent/Z Code/Z Architect mode ──► MCP tools ─────►├───  HTTP ──► MCP Server
│                                 │                      (Z Open Editor)
└─────────────────────────────────┘                           │
                                                              │
                                              z/OS via Zowe Profile
```

Bob acts as the MCP **client (host)**. The Z Open Editor extension provides the MCP **server**. The server exposes tools that Bob can call on your behalf, with your Zowe profile credentials.

> The Z Open Editor MCP server uses **Streamable HTTP transport** over `http://localhost:3005/mcp`. The server process is managed by the Z Open Editor extension and starts automatically — you only need to add the URL to your project configuration file. The port number is any unused port in your local machine

### 📁 Configuration File Locations

| Scope       | File Path                         | Applies To                     |
| -------------| -----------------------------------| --------------------------------|
| **Global**  | `~/.bob/mcp.json`                 | All workspaces on this machine |
| **Project** | `.bob/mcp.json` (in project root) | Current project only           |

> **Precedence rule:** When the same server name exists in both files, **project-level configuration takes priority**.

---

## 3. Lab Preparation
[↩️](#-table-of-contents)

### 🎯 Objective

Install the required extensions, verify your Zowe profile, and confirm your Bob IDE version before proceeding.

### ✅ Prerequisites

- IBM Bob IDE installed (macOS, Linux, or Windows) — download from https://bob.ibm.com/download
- A subscription with access to **Bob Premium Package for Z**
- Network access to a z/OS system (provided by your instructor or your own LPAR)
- Node.js 18+ installed on your workstation (required by the Z Open Editor MCP server)
- The following extensions installed and active in Bob:

| Extension                     | Tested Version |
| -------------------------------| ----------------|
| Zowe Explorer                 | 3.5.1          |
| IBM Z Open Editor             | 6.7.0          |
| IBM Bob Premium Package for Z | 3.0.21         |

- **Bob IDE tested version:** 1.126.0+bob2.1.0

### 📝 Pre-Lab: Verify Extensions

1. Open Bob IDE.
2. Click the **Extensions** icon in the Activity Bar (or press `Cmd+Shift+X` / `Ctrl+Shift+X`).
3. Confirm all three extensions listed above are installed.
4. Open the **Zowe Explorer** panel (Activity Bar) and verify your Zowe profile is listed and shows a connected status.


### 🎯 You're Ready!

Once your extensions are confirmed and your Zowe profile is connected, open the **Bob chat panel** to begin.

In the Bob chat panel, click **New Task** and select the correct Zowe profile context before starting the exercises.

---

## 4. Lab Context
[↩️](#-table-of-contents)

### The Z Open Editor MCP Server

IBM Z Open Editor ships a built-in MCP server (`zopeneditor-sample`) that exposes over 60 tools for interacting with z/OS systems. This server is started automatically by the extension when Bob connects to it — **you do not need to install or start it manually**.

However, you **do** need to register it in Bob's MCP configuration so that Bob knows it exists and can connect to it. You will do this in **Exercise 2**.

### Preliminary Remarks

> 💡 Throughout this lab, replace `<HLQ>` with your personal high-level qualifier as defined in z/OS system. Replace `<userid>` with your TSO user ID. 

---

## 5. Learning Objectives
[↩️](#-table-of-contents)

By the end of this lab, you will be able to:

- ✅ Explain what MCP is and how it extends IBM Bob's capabilities
- ✅ Identify the difference between global and project-level MCP configuration
- ✅ Register the Z Open Editor MCP server in Bob IDE using Streamable HTTP transport
- ✅ Verify that MCP tool discovery has succeeded in the Bob Settings panel
- ✅ Enable, disable, and auto-approve individual MCP tools
- ✅ Invoke mainframe MCP tools through natural language prompts in Agent mode
- ✅ Create a project-level `.bob/mcp.json` for team sharing

---

## Exercise 1: Understanding the Z Open Editor MCP Server
[↩️](#-table-of-contents)

### 🎯 Objective

Explore the tool categories provided by the Z Open Editor + Zowe MCP server and understand how they map to mainframe operations.

### 🔧 Bob Mode to Use

**Mode: 💻 Agent**

### 📝 Context

Before configuring MCP, it is useful to understand what the Z Open Editor MCP server actually provides. The server exposes tools organized into functional categories.

### ✍️ Your Task

Ask Bob to explain what MCP tools are available from the Z Open Editor server.

### 💬 Bob Prompt

```
What tools does the Z Open Editor MCP server and Zowe MCP server provide for mainframe development? Summarize them by category.
```

### ⚙️ What Bob Does Automatically

Bob will draw on its knowledge of the Z Open Editor MCP server and describe the available tool categories.

### ✅ Expected Result — Tool Categories

| Category                | Example Tools                                                                                                       |
| -------------------------| ---------------------------------------------------------------------------------------------------------------------|
| **Dataset operations**  | `zowe-list-datasets`, `zowe-list-members`, `zowe-get-dataset-content`, `zowe-upload-dataset`                        |
| **Job management**      | `zowe-submit-dataset`, `zowe-list-jobs`, `zowe-list-job-spools`, `zowe-get-all-job-spools-output`, `zowe-purge-job` |
| **TSO commands**        | `zowe-issue-tso-command`                                                                                            |
| **USS file operations** | `zowe-list-unix-files`, `zowe-get-unix-file-content`, `zowe-upload-unix-file`, `zowe-issue-unix-command`            |
| **Profile management**  | `zowe-get-default-profile`, `zowe-profile-connection-check`, `zowe-list-profiles`                                   |
| **Code analysis**       | `zopeneditor-cobol-get-data-flow`, `zopeneditor-cobol-get-program-control-flow`                                     |
| **User build (DBB)**    | `userbuild-run-user-build`, `dbb-get-build-result`                                                                  |
| **ZCodeScan**           | `zcodescan-check-current-program`, `zcodescan-check-list-of-local-programs`                                         |

### 🎓 What You Learn

- The breadth of mainframe operations that MCP unlocks for Bob
- How tool names map to the underlying Zowe and Z Open Editor capabilities
- Why controlling which tools are active matters for context window efficiency

---

## Exercise 2: Registering the Z Open Editor MCP Server in Bob
[↩️](#-table-of-contents)

### 🎯 Objective

Register the Z Open Editor MCP server in your Bob IDE project-level configuration, enable it, and confirm it connects successfully.

### 🔧 Bob Mode to Use

**Mode: ⚙️ Bob Settings Panel** (no chat prompt needed for this exercise)

### 📝 Context

Bob does not ship with any pre-installed MCP servers. You must register the Z Open Editor MCP server so that Bob can discover and invoke its tools. In this lab you use the project-level configuration file (`.bob/mcp.json` in the workspace root) so the settings travel with the project.

### ✍️ Your Task — Part A: Create the Configuration File

1. In your workspace root, create a file `.bob/mcp.json` with the following content:

```
{
  "mcpServers": {
    "zopeneditor-sample": {
      "url": "http://localhost:3005/mcp",
      "type": "streamable-http",
      "timeout": 600
    }
  }
}
```

**Save the file** (`Cmd+S` / `Ctrl+S`).

### ✍️ Your Task — Part B: Enable the Z Open Editor MCP Server

1. Open Bob IDE **Settings** and search for  `zopeneditor.mcp.enabled`.
4. Enable the setting by checking the checkbox.
5. Reload the VS Code window (`Cmd+Shift+P` / `Ctrl+Shift+P` → **Developer: Reload Window**) to ensure all components are initialised.

### ✍️ Your Task — Part C: Verify the Connection

1. From the Bob chat view, click the **⚙️ Settings** icon and switch to the **MCP** page.
2. Confirm that **zopeneditor-sample** appears in the server list with the same name as in the config file.
3. If the server shows as disconnected, click the **↻ Refresh** icon in the top-right corner. Repeat until the status turns green (connected).

### ✅ Expected Result

The MCP tab shows:

```
zopeneditor-sample   ● Connected   Workspace
```

### 🎓 What You Learn

- How to create and populate the project-level MCP configuration file (`.bob/mcp.json`)
- The required fields for a Streamable HTTP MCP server entry (`url`, `type`, `timeout`)
- How to enable the Z Open Editor MCP server and verify the connection in the Settings panel

---

## Exercise 3: Verifying MCP Tool Discovery
[↩️](#-table-of-contents)

### 🎯 Objective

Confirm that Bob has successfully discovered and loaded the Z Open Editor MCP tools, and inspect the tool list.

### 🔧 Bob Mode to Use

**Mode: ⚙️ Bob Settings Panel → MCP tab**

### 📝 Context

After a server connects, Bob automatically discovers all tools it exposes. The MCP Settings panel lets you inspect the full tool list and verify discovery completed without errors.

### ✍️ Your Task

1. In the Bob Settings → **MCP** tab, locate **zopeneditor-sample**.
2. Expand the entry 
3. Review the list of discovered tools.

### ✅ Expected Result — Tool Count by Category

You should see approximately **60–70 tools** grouped under the server. Look for tools from these key groups:

| Tool Name Prefix | What It Does |
|---|---|
| `zowe-list-*` | Lists datasets, members, jobs, Unix files |
| `zowe-get-*` | Reads dataset content, spool output, Unix files |
| `zowe-upload-*` / `zowe-download-*` | Transfers files to/from z/OS |
| `zowe-submit-*` | Submits JCL jobs |
| `zowe-issue-*` | Executes TSO and USS commands |
| `zowe-profile-*` | Manages Zowe profile connections |
| `zopeneditor-cobol-*` | COBOL analysis, data flow, control flow |
| `userbuild-*` | DBB user build integration |
| `zcodescan-*` | Static code quality scanning |

### 🎓 What You Learn

- How Bob's MCP panel surfaces the tools advertised by a connected server
- How to validate that tool discovery has completed successfully
- The naming convention used by Z Open Editor MCP tools

---

## Exercise 4: Managing MCP Tools — Enable, Disable, and Auto-Approve
[↩️](#-table-of-contents)

### 🎯 Objective

Control which Z Open Editor MCP tools Bob can use by toggling individual tools on and off, and configure auto-approval for trusted tools.

### 🔧 Bob Mode to Use

**Mode: ⚙️ Bob Settings Panel → MCP tab**

### 📝 Context

Every active MCP tool definition consumes space in Bob's context window. For large MCP servers with 60+ tools, disabling tools you don't need frees up context for your code and conversation. Additionally, by default Bob asks for confirmation before running any tool call — auto-approval lets trusted tools run without interruption.

### ✍️ Your Task — Part A: Disable Unused Tools

In the MCP Settings panel, expand **zopeneditor-sample** and disable the following tools (click the toggle next to each):

| Tool to Disable                | Reason                                     |
| --------------------------------| --------------------------------------------|
| `wazideploy-*` (all 8 tools)   | Not needed unless using Wazi Deploy        |
| `rseapi-copy-unix-cross-lpar`  | Cross-LPAR operations not used in this lab |
| `zowe-copy-dataset-cross-lpar` | Cross-LPAR operations not used in this lab |

> ✅ After disabling, the tool count should drop to approximately **50 active tools**.

### ✍️ Your Task — Part B: Enable Auto-Approval for Read-Only Tools

Configure auto-approval for tools that only read data (safe, non-destructive operations):

1. In the expanded tool list, locate `zowe-list-datasets`.
2. Check the **Always allow** checkbox next to it.
3. Repeat for: `zowe-list-members`, `zowe-get-dataset-content`, `zowe-list-unix-files`, `zowe-get-unix-file-content`, `zowe-profile-connection-check`.

> ⚠️ **Best practice:** Only auto-approve read-only tools. Always require confirmation for write, submit, and delete operations.

### ✍️ Your Task — Part C: Verify in JSON Config

Optionally, view the project-level config to see how auto-approval is represented:

1. Open **Edit Project MCP** from the MCP Settings tab.
2. Observe the `alwaysAllow` array in the server entry:

```json
{
  "mcpServers": {
    "zopeneditor-sample": {
      "alwaysAllow": [
        "zowe-list-datasets",
        "zowe-list-members",
        "zowe-get-dataset-content",
        "zowe-list-unix-files",
        "zowe-get-unix-file-content",
        "zowe-profile-connection-check"
      ]
    }
  }
}
```

### 🎓 What You Learn

- How disabling unused tools improves Bob's context efficiency
- How to configure per-tool auto-approval for safe, uninterrupted workflows
- How the `alwaysAllow` array maps to the UI toggle in the Settings panel

---

## Exercise 5: Using MCP Tools in Agent Mode
[↩️](#-table-of-contents)

### 🎯 Objective

Use Agent mode to invoke Z Open Editor MCP tools through natural language prompts and observe how Bob selects and calls the appropriate tools automatically.

### 🔧 Bob Mode to Use

**Mode: 💻 Agent**

### 📝 Context

Now that the Z Open Editor MCP server is connected and configured, Bob in Agent mode has access to all the enabled mainframe tools. The following prompts demonstrate Bob selecting and chaining MCP tool calls on your behalf.

---

### Part A: Verify Connection and Profile

#### 💬 Bob Prompt

```text
what is my current active zowe profile
```

#### ⚙️ What Bob Does Automatically

Bob calls:
1. `zopeneditor-get-default-zowe-profile` — retrieves the Z Open Editor default profile name
2. `zowe-profile-connection-check` — tests connectivity to the z/OS REST API

#### ✅ Expected Result

```
The profile used by Bob's MCP tools (e.g. for dataset browsing, job submission) is the Z Open Editor one: <profilename>
```

---

### Part B: Browse Datasets

#### 💬 Bob Prompt

```text
use profile <profilename>, List all datasets starting with <HLQ> and show me the members of <HLQ>.CBL
```

#### ⚙️ What Bob Does Automatically

Bob calls:
1. `zowe-list-datasets` with filter `<HLQ>.*`
2. `zowe-list-members` for `<HLQ>.CBL`

#### ✅ Expected Result

A list of datasets under `<HLQ>.*` followed by the member names in `<HLQ>.CBL`.

---

### Part C: Read a Dataset Member

#### 💬 Bob Prompt

```text
Show me the content of <HLQ>.CBL(HELLO)
```

#### ⚙️ What Bob Does Automatically

Bob calls:
1. `zowe-get-dataset-content` for `<HLQ>.CBL(HELLO)`
2. Displays the COBOL source inline in the chat

#### ✅ Expected Result

The COBOL source code for `HELLO` displayed in the Bob chat panel.

---

### Part D: Submit a JCL Job and Retrieve Output

#### 💬 Bob Prompt

```text
Submit the JCL job in <HLQ>.JCL(HELLO), wait for it to complete, and show me the JESMSGLG spool.
```

#### ⚙️ What Bob Does Automatically

Bob calls:
1. `zowe-submit-dataset` for `<HLQ>.JCL(HELLO)`
2. `zowe-list-jobs` to find the submitted job by name
3. `zowe-list-job-spools` to list available spool files
4. `zowe-get-single-job-spool-output` for the JESMSGLG file

#### ✅ Expected Result

```
Job submitted: COMPILE — Job ID: JOB12345
Status: CC 0000 ✅
JESMSGLG:
  ...job log output...
```


> 💡 If the job failed, ask Bob to diagnose and suggest a fix:
> ```text
> Troubleshoot why the job failed and suggest a fix for me.
> ```

---

### Part E: COBOL Code Analysis

#### 💬 Bob Prompt

```text
Analyze the control flow of the COBOL program <HLQ>.CBL(HELLO) and summarize its structure.
```

#### ⚙️ What Bob Does Automatically

Bob calls:
1. `zowe-get-dataset-content` to retrieve the source
2. `zopeneditor-cobol-get-program-control-flow` to analyze paragraph and section relationships
3. Summarizes the program structure inline

#### ✅ Expected Result

A structured summary of the COBOL program's sections, paragraphs, and control flow relationships.

### 🎓 What You Learn

- How Bob selects the correct MCP tool based on your natural language intent
- How Bob chains multiple MCP tool calls to complete a multi-step mainframe task
- The difference between pure dataset tools and code analysis tools

---

## 6. Conclusion
[↩️](#-table-of-contents)

### 🎉 Congratulations!

You have completed the IBM Bob MCP Configuration lab. You now understand how to:

- Explain the MCP architecture — client, server, transport, and tool discovery
- Register the Z Open Editor MCP server in Bob using Streamable HTTP transport
- Verify tool discovery and manage which tools are active
- Configure auto-approval for safe, high-frequency operations
- Invoke mainframe operations through natural language in Agent mode

### 🔧 Configuration Summary

| Configuration                    | File                           | Scope                              |
| ----------------------------------| --------------------------------| ------------------------------------|
| Project MCP config (team-shared) | `.bob/mcp.json`                | Current project (committed to Git) |
| Auto-approved tools              | `alwaysAllow` array in config  | Per-server, per-tool               |
| Excluded tools                   | `excludeTools` array in config | Reduces context window usage       |

### 📊 Key Concepts Recap

| Concept                  | What to Remember                                                                   |
| --------------------------| ------------------------------------------------------------------------------------|
| **MCP transport**        | Z Open Editor uses Streamable HTTP (`localhost:3005`) — local, low-latency, secure |
| **Tool discovery**       | Automatic after server connects; check MCP tab to verify                           |
| **Context efficiency**   | Disable unused tools to leave room for code and conversation                       |
| **Auto-approval**        | Only for read-only, non-destructive tools                                          |

---

**Thank you for participating in this lab!**

**Lab Version:** 1.0  
**Creation date:** 2026-09
**Author:** IBM Bob Premium Package for Z Team

---
