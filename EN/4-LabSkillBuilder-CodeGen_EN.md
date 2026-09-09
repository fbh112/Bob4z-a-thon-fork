# Lab: Advanced IBM Bob Premium Package for Z
## Skill Builder: COBOL Code Generation and Validation on CBSA

**Estimated Duration:** 1-2 hours
**Level:** Advanced
**Prerequisites:** Completion of [Lab 1 — Discovering CBSA](1-LabDiscoverCBSA_EN.md)

---

## 📋 Table of Contents

1. [Introduction](#1-introduction)
2. [Lab Preparation](#2-lab-preparation)
3. [Exercise 1: Generating a COBOL Code Standard Skill](#exercise-1-generating-a-cobol-code-standard-skill)
4. [Exercise 2: Generating a CICS Program for Phone-Based Customer Search](#exercise-2-generating-a-cics-program-for-phone-based-customer-search)
5. [Exercise 3: Generating a CICS Program with BMS Map](#exercise-3-generating-a-cics-program-with-bms-map)
6. [Conclusion](#4-conclusion)

---

## 1. Introduction

[↩️](#-table-of-contents)

This lab continues directly from **Lab 1 — Discovering CBSA**. You will reuse the same CBSA workspace, the same source programs, and the metadata database that Bob built during Lab 1.

Two advanced capabilities of IBM Bob Premium Package for Z are explored here:

1. **Custom COBOL Code Generation Skill** — extract the coding standards already present in the CBSA codebase and package them into a reusable Bob skill so that all future code generation automatically enforces those standards and validates syntax using Z Open Editor.
2. **COBOL Program Generation** — use the skill to generate new, standards-compliant CICS programs for the CBSA application, covering both a simple VSAM search program and a full pseudo-conversational BMS map program.

> **Note on Z Open Editor syntax validation:** Once a generated COBOL file is open in the editor, Z Open Editor validates its syntax automatically. You can also customize the linting rules via ZCodeScan (see [IBM documentation](https://www.ibm.com/docs/en/developer-for-zos/17.0.x?topic=overview-linting-zcodescan)).

---

## 2. Lab Preparation

[↩️](#-table-of-contents)

### ✅ Prerequisites


| Requirement                         | Details                                                             |
| ----------------------------------- | ------------------------------------------------------------------- |
| Lab 1 completed                     | Workspace initialized, `AGENTS.md` created, metadata database built |
| CBSA source code                    | Cloned at `~/CBSA/CBSA-base-source/`                                |
| `AGENTS.md` present                 | At the workspace root                                              |
| `.bobz/local-settings.json` present | Metadata database from Lab 1 Exercise 1                            |
| Bob extensions                      | Same versions as Lab 1                                             |

### 📝 Resuming the Workspace

Open Bob IDE and reopen the `~/CBSA` folder (**File → Open Folder**).
Click the **`+` icon** in the Bob panel header to start a fresh conversation, then confirm the workspace is still recognized:

```text
Show me the current workspace context from @AGENTS.md
```

Bob should summarize the CBSA application description, the metadata database location, and the `docs/` output convention established in Lab 1. If `AGENTS.md` appears incomplete, re-run `/init` before continuing.

> **Note:** IBM Bob relies on AI agentics, which is non-deterministic. Your results may differ slightly from the samples shown here. If Bob takes a different path, guide it with follow-up prompts — it will reach the correct outcome.

---

## Exercise 1: Generating a COBOL Code Standard Skill

> **⚠️ Coming from Lab 1?** In Lab 1 Exercise 2 Step 2, you ran `/z-coding-standards-skill-builder` and replied **No** to ZCodeScan validation (to keep that lab simple). **This lab requires ZCodeScan to be included in the skill.** You must rebuild it — follow Step 1 below and reply **Yes** when Bob asks about ZCodeScan.

### 🎯 Objective

Extract the coding standards already present in the CBSA COBOL programs and package them into a persistent Bob skill. Once created, the skill is automatically applied in every future conversation involving code generation, code review, or syntax validation.

### 🔧 Bob Mode to Use

**Mode: 🧰 Z Code**

Z Code mode specializes in analyzing and documenting mainframe applications (COBOL, PL/I, JCL, Assembler, REXX).

---

### Step 1 — Generate the Coding Standards Skill from the CBSA Codebase

#### 💬 Bob Prompt

```text
/z-coding-standards-skill-builder
```

When Bob asks whether to include **ZCodeScan validation** in the skill, reply **Yes** — this lab uses Z Open Editor syntax validation as part of the generation workflow.

#### ⚙️ What Bob Does Automatically

Bob will:

1. Scan all COBOL programs in `CBSA-base-source/cobol_src/`
2. Analyze naming conventions (programs, variables, paragraphs)
3. Identify standard structure patterns (WORKING-STORAGE, PROCEDURE DIVISION layout)
4. Extract error handling patterns (CICS RESP, SQLCODE handling)
5. Document CICS and SQL interaction patterns including compilation directives
6. Generate three files under `.bob/skills/cbsa-coding-standards/`:
   - `SKILL.md` — skill definition used by Bob
   - `references/CBSA-standards-reference.md` — full standards documentation
   - `references/CBSA-example.md` — annotated compliant code examples

#### ✅ Sample Result

```
Created: .bob/skills/cbsa-coding-standards/SKILL.md
Created: .bob/skills/cbsa-coding-standards/references/CBSA-standards-reference.md
Created: .bob/skills/cbsa-coding-standards/references/CBSA-example.md
```

The skill captures CBSA patterns such as:

- Program names: 7-character uppercase identifiers (e.g., `BNK1CAC`, `BNKCUST`)
- Paragraph names: hyphen-separated uppercase (e.g., `VALIDATE-INPUT`, `PROCESS-ERROR`)
- WS variables: prefixed with `WS-` followed by a descriptive name (e.g., `WS-CUSTOMER-ID`)
- Error handling: `RESP` / `RESP2` checks after every CICS command
- SQL: `SQLCODE` check after every `EXEC SQL`, with `ABEND` on unexpected codes
- COMMAREA: always defined as a copybook, length checked at entry
- Compilation directives: `PROCESS CICS,NODYNAM,NSYMBOL(NATIONAL),TRUNC(STD)` for CICS programs, with `CBL SQL` added for DB2

At the end of Bob's output, it will show activation examples such as "generate code", "review code", "check standards". You can reference the skill in any future prompt.

---

### Step 2 — Create the COBOL Code Generator Skill

With the coding standards captured, build a second skill that uses them to generate and validate COBOL programs automatically.

#### 💬 Bob Prompt

```text
Create a Bob Premium for Z skill, using the skill builder, that allows to:
- generate a COBOL program that respects the coding standards defined in current workspace
- then verify using Z Open Editor, the syntax and standard of the generated COBOL. Syntax errors must be corrected by Bob.
```

#### ⚙️ What Bob Does Automatically

Bob uses the `create-skill` skill to create a new `cobol-generator-validator` skill (or similar name) in `.bob/skills/` containing:

- `SKILL.md` — skill definition and activation instructions

#### ✅ Sample Result

```
Created: .bob/skills/cobol-generator-validator/SKILL.md
```

The skill is ready to use. It activates whenever your prompt contains phrases such as:
- "Generate a COBOL program"
- "Create a COBOL program"
- "Validate COBOL"

Bob will then automatically apply the CBSA coding standards during generation and invoke Z Open Editor validation — correcting any syntax errors before returning the final program.

---

### 🎓 Key Takeaways — Exercise 1

- **Automation**: Bob extracts standards directly from your existing workspace codebase — no manual survey needed.
- **Reusability**: The skill persists across all conversations and can be shared with the team by committing `.bob/skills/` to version control.
- **Living standard**: Update the skill whenever conventions evolve — one prompt is enough.

---

## Exercise 2: Generating a CICS Program for Phone-Based Customer Search

[↩️](#-table-of-contents)

### 🎯 Objective

Use the new created code generator skill to generate a new, standards-compliant CICS COBOL program that finds a customer's accounts from their phone number, then automatically validate it with Z Open Editor.

### 🔧 Bob Mode to Use

**Mode: 🧰 Z Code**

Z Code mode excels at generating COBOL code that respects CBSA coding standards and at iteratively correcting syntax errors detected by Z Open Editor.

### 📝 Context

The CBSA application currently identifies customers only by customer number. Adding phone-based search is a realistic extension: it requires reading the `CUSTOMER` VSAM file using an alternate key, validating the phone field format, and returning a `COMMAREA` response consistent with other CBSA inquiry programs.

The skills built in Exercise 1 ensure the generated program immediately follows CBSA naming, structure, and error handling conventions — and that syntax is validated automatically by Z Open Editor — without any manual rework.

### ✍️ Your Task

Write your own prompt to request generation of the phone search program. After generation, open the file in the editor and observe Z Open Editor's inline syntax diagnostics.

**Expected in your prompt:**

- name the target program and its purpose
- reference the coding standards skill explicitly
- request COMMAREA-based input/output interface
- request automatic syntax validation and correction

### 💬 Bob Prompt

```text
Generate a COBOL program that allows finding a customer's accounts from their phone number.
Follow the cbsa-coding-standards skill for naming conventions, structure, and error handling.
After generation, verify the syntax with Z Open Editor and correct any detected errors.
```

### ⚙️ What Bob Does Automatically

Bob will:

1. Trigger the `cobol-generator-validator` skill
2. Query the metadata database to inspect the `CUSTOMER` copybook structure and locate the phone field
3. Apply the `cbsa-coding-standards` skill for naming, structure, and error handling
4. Generate a new CICS COBOL program and copybook compliant with coding standards
5. Open the generated file in Z Open Editor
6. Read Z Open Editor diagnostics and iterate to correct any syntax errors

### ✅ Sample Result

Bob generates `CBSA-base-source/cobol_src/INQPHONE.cbl` and `CBSA-base-source/cobol_copy/INQPHONE.cpy`.
Z Open Editor reports zero diagnostics after Bob's automatic correction pass.

If Bob does not correct all errors automatically, prompt it explicitly:

```text
Z Open Editor reports errors in @INQPHONE.cbl — please review and fix them
```

---

### 🎓 Key Takeaways — Exercise 2

- **Metadata-driven generation**: Bob queries the local SQLite database to resolve the exact `CUSTOMER` copybook structure before writing a single line of code — no guessing.
- **Standard enforcement**: The coding standard skill is applied automatically — the generated program is immediately compliant without manual rework.
- **Integrated validation**: Z Open Editor provides real-time syntax feedback, and Bob can consume that feedback to self-correct in the same conversation.

---

## Exercise 3: Generating a CICS Program with BMS Map

[↩️](#-table-of-contents)

### 🎯 Objective

Use the code generator skill to generate a complete pseudo-conversational CICS COBOL program that displays and updates bank account information via a BMS map, then validate it with Z Open Editor.

### 🔧 Bob Mode to Use

**Mode: 🧰 Z Code**

Z Code mode excels at generating COBOL code for Z/OS, including full pseudo-conversational CICS patterns with BMS SEND/RECEIVE.

### 📝 Context

BMS-based programs are the core of the CBSA user interface. A pseudo-conversational program stores its state across terminal interactions using a `COMMAREA`, processes user input from a BMS map, and handles edge cases like `MAPFAIL`. This exercise generates a new account update screen program that follows the same structure as existing CBSA presentation programs (`BNK1CAC`, `BNK1DAC`).

### ✍️ Your Task

Write your own prompt to request a BMS map program. Specify the program name, the screen's purpose, and the interaction pattern.

**Expected in your prompt:**

- specify the program name (`UPDTACCT`)
- describe the screen purpose (display and update account information)
- request pseudo-conversational pattern
- reference the coding standards skill

### 💬 Bob Prompt

```text
Create a COBOL program that uses a BMS map 
to display and update bank account information.
The program should be called UPDTACCT and use a pseudo-conversational pattern.

```

### ⚙️ What Bob Does Automatically

Bob will:

1. Trigger the `cobol-generator-validator` skill
2. Inspect existing BMS map programs (`BNK1CAC.cbl`, `BNK1DAC.cbl`) in the metadata database for structural patterns
3. Apply the `cbsa-coding-standards` skill for naming, structure, and error handling
4. Generate `CBSA-base-source/cobol_src/UPDTACCT.cbl` with pseudo-conversational COMMAREA, BMS SEND/RECEIVE, MAPFAIL handling, and input validation
5. Generate `CBSA-base-source/cobol_bms/UPDTACCT.bms` with the corresponding screen definition
6. Open the generated files in Z Open Editor and iterate to resolve any diagnostics

### ✅ Sample Result

Bob generates `CBSA-base-source/cobol_src/UPDTACCT.cbl` and `CBSA-base-source/cobol_bms/UPDTACCT.bms`.
Z Open Editor reports zero diagnostics after Bob's correction pass.

If errors still remain after generation, guide Bob with:

```text
Z Open Editor reports errors in @UPDTACCT.cbl — please review and fix them
```

---

### 🎓 Key Takeaways — Exercise 3

- **BMS generation**: Bob produces both the COBOL program and the BMS map source in one step
- **Pseudo-conversational pattern**: COMMAREA mode flag, SEND/RECEIVE MAP, and MAPFAIL handling are generated correctly following CBSA conventions
- **Same quality loop**: Z Open Editor validation and automatic correction apply identically to BMS programs as to VSAM programs

---

## 💡 Added Value — Exercises 2 & 3

| Aspect                              | Manual Approach       | With Bob                    |
| ----------------------------------- | --------------------- | --------------------------- |
| Standards analysis before coding    | 2–3 days              | Already done (Exercise 1)   |
| CICS VSAM search program            | 2–3 days              | 10 minutes                  |
| BMS pseudo-conversational program   | 3–5 days              | 15 minutes                  |
| Syntax validation & correction      | Manual compiler cycle | Automatic in-conversation   |

**Overall gain: ~90–95% reduction in time**

---

## 4. Conclusion

[↩️](#-table-of-contents)

### 🎉 Congratulations!

You have completed the IBM Bob Premium Package for Z COBOL Code Generation lab. Building directly on the CBSA workspace from Lab 1, you have:

- Generated a **persistent coding standard skill** (`cbsa-coding-standards`) from the CBSA codebase
- Generated a **persistent code generator skill** (`cobol-generator-validator`) from the CBSA codebase
- Generated a **standards-compliant CICS VSAM search program** (`INQPHONE.cbl`, `INQPHONE.cpy`) with automatic Z Open Editor validation
- Generated a **pseudo-conversational BMS map program** (`UPDTACCT.cbl`, `UPDTACCT.cpy`, `UPDTACCT.bms`) with input validation and full error handling

### 🔧 Mode Usage Summary


| Exercise                           | Mode Used | Reason for Choice                                         |
| ---------------------------------- | --------- | --------------------------------------------------------- |
| 1. Coding Standards Skill          | 🧰 Z Code | Pattern extraction and skill generation from COBOL source |
| 2. Phone Search Program Generation | 🧰 Z Code | COBOL code generation with Z Open Editor validation       |
| 3. BMS Map Program Generation      | 🧰 Z Code | Pseudo-conversational CICS program generation             |

### 📚 Created Resources

| Document                                           | Content                                      |
| -------------------------------------------------- | -------------------------------------------- |
| `.bob/skills/cbsa-coding-standards/SKILL.md`       | Reusable coding standard skill               |
| `.bob/skills/cbsa-coding-standards/references/`    | Full CBSA standards documentation            |
| `.bob/skills/cobol-generator-validator/SKILL.md`   | COBOL generation and validation skill        |
| `CBSA-base-source/cobol_src/INQPHONE.cbl`          | Phone-based customer search program          |
| `CBSA-base-source/cobol_copy/INQPHONE.cpy`         | Phone-based customer search copybook         |
| `CBSA-base-source/cobol_src/UPDTACCT.cbl`          | Pseudo-conversational account update program |
| `CBSA-base-source/cobol_bms/UPDTACCT.bms`          | BMS screen definition for UPDTACCT          |

---

**Lab Version:** 2.0
**Date:** 2026-09
**Author:** IBM Bob Premium Package for Z Team
**Prerequisite:** [Lab 1 — Discovering IBM Bob Premium Package for Z](1-LabDiscoverCBSA_EN.md)
