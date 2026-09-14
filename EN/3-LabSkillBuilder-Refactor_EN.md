# Lab: Advanced IBM Bob Premium Package for Z
## Skill Builder: Coding Standards & VSAM to DB2 Refactoring on CBSA

- **Estimated Duration:** 1-2 hours  
- **Level:** Advanced
- **Prerequisites:** Completion of [Lab 1 — Discovering IBM Bob Premium Package for Z](1-LabDiscoverCBSA_EN.md)

---

## 📋 Table of Contents

1. [Introduction](#1-introduction)
2. [Lab Preparation](#2-lab-preparation)
3. [Exercise 1: Generating a Custom Coding Standard Skill *(optional if done in Lab 1)*](#exercise-1-generating-a-custom-coding-standard-skill-optional-if-done-in-lab-1)
4. [Exercise 2: VSAM to DB2 Refactoring of CRECUST](#exercise-2-vsam-to-db2-refactoring-of-crecust)
5. [Conclusion](#3-conclusion)

---

## 1. Introduction

[↩️](#-table-of-contents)

This lab continues directly from **Lab 1 — Discovering IBM Bob Premium Package for Z**. You will reuse the same CBSA workspace, the same source programs, and the metadata database that Bob built during Lab 1.

Two advanced capabilities of IBM Bob Premium Package for Z are explored here:

1. **Custom Coding Standard Skill** — automatically extract the coding patterns already present in the CBSA codebase and package them into a reusable Bob skill so that all future code generation and code review enforces those standards automatically.
2. **VSAM to DB2 Refactoring** — use Bob's interactive `/refactor` workflow to modernize `BNKCUST.cbl`, replacing all CICS VSAM commands with DB2 SQL while preserving functional compatibility and the existing COMMAREA interface.

---

## 2. Lab Preparation

[↩️](#-table-of-contents)

### ✅ Prerequisites


| Requirement                         | Details                                                             |
| -------------------------------------| ---------------------------------------------------------------------|
| Lab 1 completed                     | Workspace initialized, `AGENTS.md` created, metadata database built |
| CBSA source code                    | Cloned at `~/CBSA/CBSA-base-source/`                                |
| `AGENTS.md` present                 | At the workspace root                                               |
| `.bobz/local-settings.json` present | Metadata database from Lab 1 Exercise 1                             |
| Bob extensions                      | Same versions as Lab 1                                              |

### 📝 Resuming the Workspace

Open Bob IDE and reopen the `~/CBSA` folder (**File → Open Folder**).
Click the **`+` icon** in the Bob panel header to start a fresh conversation, then confirm the workspace is still recognized:

```text
Show me the current workspace context from @AGENTS.md
```

Bob should summarize the CBSA application description, the metadata database location, and the `docs/` output convention established in Lab 1. If `AGENTS.md` appears incomplete, re-run `/init` before continuing.

> **Note:** IBM Bob relies on AI agentics, which is non-deterministic. Your results may differ slightly from the samples shown here. If Bob takes a different path, guide it with follow-up prompts — it will reach the correct outcome.

---

## Exercise 1: Generating a Custom Coding Standard Skill *(optional if done in Lab 1)*

> **⏩ Already done this in Lab 1?** If you completed Lab 1 Exercise 2 Step 2 (`/z-coding-standards-skill-builder`) and the skill file `.bob/skills/cbsa-coding-standards/SKILL.md` already exists in your workspace, you can **skip this exercise and go directly to [Exercise 2](#exercise-2-vsam-to-db2-refactoring-of-bnkcust)**. The skill Bob needs for code generation and review is already in place.

### 🎯 Objective

Extract the coding patterns already present in the CBSA COBOL programs and package them into a persistent Bob skill. Once created, the skill is automatically applied in every future conversation: Bob will use it when generating new code, reviewing existing code, or refactoring programs.

### 🔧 Bob Mode to Use

**Mode: 🧰 Z Code**

---

### Step 1 — Generate the Skill from the CBSA Codebase

#### 💬 Bob Prompt

```text
/z-coding-standards-skill-builder
```

When Bob asks whether to include **ZCodeScan validation** in the skill, reply **No** (for simplicity in the lab).

#### ⚙️ What Bob Does Automatically

Bob will:

1. Scan all COBOL programs in `CBSA-base-source/cobol_src/`
2. Analyze naming conventions (programs, variables, paragraphs)
3. Identify standard structure patterns (WORKING-STORAGE, PROCEDURE DIVISION layout)
4. Extract error handling patterns (CICS RESP, SQLCODE handling)
5. Document CICS and SQL interaction patterns
6. Generate three files under `.bob/skills/cbsa-coding-standards/`:
   - `SKILL.md` — skill definition used by Bob
   - `references/....md` — full standards documentation

#### ✅ Sample Result

```
Created: .bob/skills/cbsa-coding-standards/SKILL.md
Created: .bob/skills/cbsa-coding-standards/references/....md
```

The skill captures CBSA patterns such as:

- Program names: 7-character uppercase identifiers (e.g., `BNK1CAC`, `BNKCUST`)
- Paragraph names: hyphen-separated uppercase (e.g., `VALIDATE-INPUT`, `PROCESS-ERROR`)
- Error handling: `RESP` / `RESP2` checks after every CICS command
- SQL: `SQLCODE` check after every EXEC SQL, with `ABEND` on unexpected codes
- COMMAREA: always defined as a copybook, length checked at entry

At the end of Bob's output, it will show activation examples — e.g., "generate code", "review code", "check standards". You can use the skill in any future prompt.

---

### Step 2 — Customize the Skill (Optional)

If your organization has specific rules beyond what Bob detected, add them now.

#### 💬 Bob Prompt

```text
Modify the coding standard skill to add the following rules:
- Counter variables must start with CTR-
- Validation paragraphs must end with -VALIDATION
- Add a section on logging standards using CICS WRITEQ TD
```

#### ⚙️ What Bob Does Automatically

Bob updates the coding standard files and `SKILL.md` to include them.

---

### Step 3 — Test the Skill on Existing CBSA Code

Verify that the skill correctly identifies conformance and violations in an existing CBSA program.

#### 💬 Bob Prompt

```text
verify @BNK1CAC.cbl is respecting cbsa coding standards
```

#### ✅ Sample Result

Bob uses the skill to produce a compliance report listing:

- Paragraphs and variables that conform to CBSA standards
- Any deviations with the specific rule violated
- Suggested corrections

For targeted improvement on specific lines:

```text
Improve the following code from CBSA-base-source/cobol_src/BNK1CAC.cbl:239-254
```

---

### Step 4 — Generate New Code Using the Skill

Now use the skill to generate a new, standards-compliant COBOL program for the CBSA application.

#### 💬 Bob Prompt

```text
Create a COBOL program for the CBSA application that:
- Searches for a customer by email address
- Validates the email format
- Returns the customer information via COMMAREA if found
- Handles all possible errors (not found, invalid input, CICS failures)

Follow the cbsa-coding-standards skill for naming conventions, structure, and error handling.
```

#### ⚙️ What Bob Does Automatically

Bob will:

1. Load `.bob/skills/cbsa-coding-standards/SKILL.md`
2. Apply all CBSA naming and structural standards
3. Generate a complete, compliant COBOL program (e.g., `CBSA-base-source/cobol_src/INQEMAIL.cbl`)
4. Add appropriate inline comments following CBSA documentation patterns

---

## 🎓 Key Takeaways — Exercise 1

- **Automation**: Bob extracts standards directly from your existing production codebase — no manual survey needed.
- **Reusability**: The skill persists across all conversations and can be shared with the team by committing `.bob/skills/` to version control.
- **Living standard**: Update the skill whenever conventions evolve — one prompt is enough.

---

## Exercise 2: VSAM to DB2 Refactoring of CRECUST

### 🎯 Objective

Modernize `CRECUST.cbl` — the CBSA create customer program — by replacing all CICS VSAM file commands with DB2 SQL, while preserving the existing COMMAREA interface and functional behavior.

### 🔧 Bob Mode to Use

- **📐 Z Architect** — for analysis, DB2 design, and migration planning
- **🧰 Z Code** — for interactive refactoring with `/refactor`

---

### Step 0 — Create the `extractionGoal.md` Refactoring Principles Document

Before any code change, document the principles that will govern the migration. This document will guide the interactive `/refactor` workflow in Step 3.

#### 💬 Bob Prompt (Mode: 📐 Z Architect)

```text
Create a document extractionGoal.md that defines the refactoring principles for migrating CRECUST.cbl from VSAM to DB2, including:

1. Functionality Preservation — all COMMAREA inputs/outputs must remain identical
2. Mapping VSAM Operations to SQL — READ→SELECT, WRITE→INSERT, REWRITE→UPDATE, DELETE→DELETE, STARTBR/READNEXT→CURSOR
3. Transaction Management — COMMIT/ROLLBACK aligned with CICS syncpoints
4. Sequential Access Optimization — DB2 cursors replacing STARTBR/READNEXT patterns
5. Robust Error Handling — SQLCODE checks after every EXEC SQL, mirroring existing RESP checks
6. Use of Host Variables — copybook fields used directly as DB2 host variables
7. Performance Optimization — appropriate DB2 indexes to match current VSAM key access
8. Structure and Modularity — paragraph-level mapping of original VSAM logic
9. Compatibility and Progressive Migration — COMMAREA interface unchanged
10. Documentation and Traceability — inline comments referencing the original VSAM operation replaced

Also include a refactoring checklist and measurable success criteria.
```

#### ⚙️ What Bob Does Automatically

Bob creates `extractionGoal.md` (approximately 400–500 lines) containing:

- 10 detailed principles with COBOL/SQL code examples drawn from the CBSA codebase
- VSAM→DB2 operation mapping tables specific to `CRECUST.cbl`
- A 3-phase migration checklist (Preparation / Refactoring / Validation)
- Measurable success criteria (functional equivalence, performance parity)

---

### Step 1 — Analyze the Existing VSAM Operations in CRECUST

#### 💬 Bob Prompt (Mode: 📐 Z Architect)

```text
Analyze CBSA-base-source/cobol_src/CRECUST.cbl and identify:
- All CICS VSAM commands used (READ, WRITE, REWRITE, DELETE, STARTBR, READNEXT, ENDBR)
- The data structures being manipulated (copybooks from cobol_copy/)
- The VSAM file names and their access keys
- The data access patterns (random vs sequential)
- The complexity points for DB2 migration
```

#### ⚙️ What Bob Does Automatically

Bob uses `execute_sql_query` against the metadata database built in Lab 1 to:

1. Locate all CICS VSAM commands in `CRECUST.cbl` without slow file scanning
2. Resolve the copybooks included (from `CBSA-base-source/cobol_copy/`)
3. Identify primary and alternate keys used
4. Document access patterns (keyed random, sequential browse)
5. Assess migration complexity per operation type

#### ✅ Sample Result

Bob produces a detailed analysis report with:

- VSAM file inventory (file name, type, key structure, record length)
- Operation inventory (READ, STARTBR/READNEXT, REWRITE, WRITE, DELETE — with paragraph locations)
- Migration complexity table per operation
- Recommendations (DB2 table structure, index strategy, cursor design)

---

### Step 2 — Design the DB2 Data Model

#### 💬 Bob Prompt (Mode: 📐 Z Architect)

```text
Propose a DB2 data model to replace the CUSTOMER VSAM file used in CRECUST.cbl, including:
- The complete table DDL definition mapped from the CUSTOMER copybook
- The necessary indexes (primary key + secondary keys matching VSAM alternate indexes)
- Integrity constraints (NOT NULL, CHECK, DEFAULT)
- Performance considerations relevant to the CBSA access patterns
```

#### ⚙️ What Bob Does Automatically

Bob:

1. Reads the `CUSTOMER.cpy` copybook from `CBSA-base-source/cobol_copy/`
2. Maps each COBOL field to an appropriate DB2 column type
3. Generates complete DDL scripts (`CREATE TABLE`, `CREATE INDEX`, constraints)
4. Documents design decisions (type widths, null handling, index selection rationale)

#### ✅ Sample Result

Bob generates:

- `docs/CRECUST-DB2-model.md` — complete guidance for data model
- Indexes on `CUSTOMER-ID` (primary), `SORT-CODE` + `CUSTOMER-ID` (composite), and any alternate key defined in VSAM
- `CHECK` constraints matching existing COBOL validation rules
- Commentary explaining each design choice

---

### Step 3 — Interactive Refactoring with `/refactor`

#### 💬 Bob Prompt (Mode: 🧰 Z Code)

```text
/refactor @CRECUST.cbl @extractionGoal.md
```

**Parameters:**

- `/refactor` — the interactive refactoring skill command from IBM Bob Premium Package for Z
- `CRECUST.cbl` — the source file to refactor
- `extractionGoal.md` — the migration principles document created in Step 0

#### ⚙️ What Bob Does Automatically

Bob launches the **interactive refactoring workflow** in 4 phases:

**Phase 1 — Analysis**

- Detects all VSAM operations in `CRECUST.cbl`
- Loads the 10 principles from `extractionGoal.md`
- Proposes a complete refactoring plan for review

**Phase 2 — Validation**

- Presents each VSAM→SQL transformation (e.g., `EXEC CICS READ` → `EXEC SQL SELECT`)
- Requests your confirmation before applying each change

**Phase 3 — Generation**

- Replaces `READ` with `SELECT … INTO`
- Replaces `STARTBR` / `READNEXT` / `ENDBR` with `DECLARE CURSOR` / `OPEN` / `FETCH` / `CLOSE`
- Replaces `WRITE` with `INSERT`
- Replaces `REWRITE` with `UPDATE … WHERE`
- Replaces `DELETE` with `DELETE … WHERE`
- Adds `SQLCODE` error handling paragraphs mirroring existing `RESP` checks
- Adds `EXEC CICS SYNCPOINT` / `ROLLBACK` where appropriate

**Phase 4 — Final Report**

- Generates a BEFORE / AFTER comparison report
- Documents every change with traceability to the original operation

#### ✅ Sample Result

Refactored `CBSA-base-source/cobol_src/CRECUST.cbl` with:

- All CICS VSAM commands replaced by `EXEC SQL` statements
- DB2 cursor logic for all sequential browse operations
- Complete `SQLCODE`-based error handling
- `EXEC CICS SYNCPOINT ROLLBACK` on failure paths
- Original `COMMAREA` interface and paragraph structure preserved
- Inline comments referencing the replaced VSAM operation

---

### Step 4 — Migration Plan and Testing Strategy

#### 💬 Bob Prompt (Mode: 📐 Z Architect)

```text
Create a complete migration plan to move CRECUST.cbl from VSAM to DB2, including:
- The migration steps
- The testing strategy (unit, integration, performance)
- The rollback plan
- Performance considerations specific to the CBSA environment
- The final validation checklist
```

#### ⚙️ What Bob Does Automatically

Bob generates a structured migration plan in 5 phases:

1. **Preparation** — Create the DB2 table and indexes (DDL from Step 2), migrate existing VSAM data
2. **Development** — Apply the refactored `CRECUST.cbl`, update the JCL for DB2 bind
3. **Integration Testing** — Functional tests (existing CBSA transactions), regression tests against Lab 1 inventory
4. **Deployment** — Cutover procedure with rollback trigger conditions
5. **Stabilization** — DB2 monitoring, performance baseline comparison

**File created:** `docs/CRECUST-VSAM-DB2-migration-plan.md`

---

## 🎓 Key Takeaways — Exercise 2

### ✅ Migration Benefits

- **Performance**: DB2 is optimized for complex queries, joins, and aggregations
- **Integrity**: ACID constraints and referential integrity enforced at the database level
- **Integration**: Standard SQL access from web, API, and analytics layers
- **Maintainability**: Familiar SQL patterns easier to understand than CICS VSAM commands

### ⚠️ Points of Attention

1. Test under realistic production load before go-live
2. The `COMMAREA` interface must remain bit-for-bit identical to avoid breaking callers
3. Properly scope `COMMIT` / `ROLLBACK` to CICS unit-of-work boundaries
4. Monitor DB2 explain plans and buffer pool usage after migration
5. Keep the VSAM fallback path available until DB2 stability is confirmed in production

### 💡 Best Practices

- **One program at a time**: Migrate and validate `CRECUST.cbl` fully before moving to the next VSAM program
- **Use the metadata database**: Bob's `execute_sql_query` queries give accurate dependency scope — more reliable than grep
- **Commit the refactoring goal document**: `extractionGoal.md` is a reusable artifact for other VSAM programs in the CBSA application

---

## 3. Conclusion

[↩️](#-table-of-contents)

### 🎉 Congratulations!

You have completed the IBM Bob Premium Package for Z Skill Builder lab. Building directly on the CBSA workspace from Lab 1, you have:

- Generated a **persistent coding standard skill** from the CBSA codebase
- Used the skill to **validate existing code** (`BNK1CAC.cbl`) and **generate new CBSA-compliant code**
- Designed a **DB2 data model** to replace the CUSTOMER VSAM file used by `CRECUST.cbl`
- Used the **/refactor interactive workflow** to modernize `CRECUST.cbl` from VSAM to DB2
- Produced a complete **migration plan** with testing and rollback strategy

### 🔧 Mode Usage Summary


| Exercise                       | Mode Used       | Reason for Choice                                          |
| ------------------------------ | --------------- | ---------------------------------------------------------- |
| 1. Coding Standards Skill      | 🧰 Z Code       | Pattern extraction and skill generation from COBOL source  |
| 2. VSAM Analysis & DB2 Design  | 📐 Z Architect  | Dependency analysis, schema design, migration planning     |
| 2. Interactive Refactoring     | 🧰 Z Code       | COBOL code transformation with `/refactor` skill command   |

### 📚 Created Resources


| Document                                        | Content                           |
| ----------------------------------------------- | --------------------------------- |
| `.bob/skills/cbsa-coding-standards/SKILL.md`    | Reusable coding standard skill    |
| `.bob/skills/cbsa-coding-standards/references/` | Full CBSA standards documentation |
| `extractionGoal.md`                             | VSAM→DB2 migration principles     |
| `docs/CRECUST-DB2-model.md`                     | DB2 data model for CUSTOMER table |
| `CBSA-base-source/cobol_src/CRECUST.cbl`        | Refactored DB2 version            |
| `docs/CRECUST-VSAM-DB2-migration-plan.md`       | Complete migration plan           |

---

**Lab Version:** 2.0
**Date:** 2026-09
**Author:** IBM Bob Premium Package for Z Team
**Prerequisite:** [Lab 1 — Discovering IBM Bob Premium Package for Z](1-LabDiscoverCBSA_EN.md)
