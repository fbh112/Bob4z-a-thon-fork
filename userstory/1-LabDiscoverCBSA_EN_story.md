# Lab: On boarding a new developer into CBSA application

## Discover, document, and change a CICS banking application with IBM Bob Premium Package for Z

**Estimated duration:** 3–4 hours  
**Level:** Intermediate  
**Audience:** Developers, architects, and technical leads joining a mainframe application team

---

## Lab story

It is your first week on the team. You have been given access to the **CICS Banking Sample Application (CBSA)** — a live banking system running on z/OS — and handed your first real assignment: the business wants to expand, and the application needs to support **multiple bank branches**.

Right now, CBSA is hardcoded to a single branch. Every transaction, every customer record, every account is tied to one fixed sort code. The ask sounds straightforward — make the sort code variable so different branches can operate independently. But before you write a single line of code, you need to understand what you are actually dealing with.

> **What is a sort code?** A sort code is a unique identifier assigned to a specific branch of a bank. It is used to route transactions to the correct location, separate from the code that identifies the bank itself. Depending on the country, the same concept goes by different names — sort code (UK), routing number (US), IFSC code (India), or branch code elsewhere. In CBSA, it is stored as a fixed constant called `SORTCODE`.

The source code is there, but documentation is thin and the original developers have moved on. So you start with three basic questions:
- What does this system actually do?
- How do its pieces fit together?
- What breaks if the sort code changes?

That is exactly what this lab walks you through. Session by session, you will go from a blank workspace to a clear picture of the application — its programs, its data, its business rules, and the full blast radius of the multi-branch change. Then, once the plan is approved, you will use IBM Bob to generate the code changes and implement the first slice of the feature.

No prior knowledge of CBSA is assumed. All you need is curiosity and the willingness to ask good questions.

## Learning outcomes

By the end of this lab, you will know how to:

- **find your way around** — set up a local workspace, map the application's programs and data, and locate what you need quickly;
- **understand before you change** — read unfamiliar code, trace a feature end to end, and extract the business rules behind it;
- **check what the change affects** — identify every program and data structure affected by the multi-branch `SORTCODE` change before touching anything;
- **plan before you code** — turn a business request into a structured plan your team lead can review and approve; and
- **implement with confidence** — use IBM Bob to generate the code changes, review the output, and validate it against the plan before committing.

## Application background

CBSA simulates bank-teller operations on IBM z/OS:

- COBOL and CICS programs, 3270/BMS screens, copybooks, Db2, and VSAM;
- customer and account creation, inquiry, maintenance, transfer, debit, and credit operations;
- Multi-tier architecture (presentation, business logic, data)

You are working from local source only. If Bob asks whether to use a centralized metadata service such as `Z Understand`, choose **local workspace analysis** for this lab.

> **Important — working with AI-assisted analysis**
>
> - **Non-determinism.** Bob uses AI, which is not deterministic. Results may differ slightly between runs, and you may occasionally need to rephrase a prompt to steer toward the expected outcome. This is normal: Bob will reach the result, but the path may vary.
> - **Counts and estimates.** AI-generated counts can be inaccurate. Treat them as starting points and validate through engineering review.
> - **Options and choices.** Bob may offer multiple options in response to a prompt. Choose the option that best fits the exercise goal. You can always restart a task and try a different option.

## Lab preparation

Before starting the lab, make sure the following are in place on your workstation:

- IBM Bob Version 2 installed (macOS, Linux, or Windows) — download from https://bob.ibm.com/download
- You have signed up for a Bob trial and have access to Bob Premium Package for Z — sign up at https://bob.ibm.com/trial
- Git and internet access available
- The following extensions installed on Bob IDE:

| Extension                     | Tested version |
| -------------------------------| ----------------|
| Zowe Explorer                 | 3.5.1          |
| IBM Z Open Editor             | 6.6.1          |
| IBM Bob Premium Package for Z | 3.0.10         |
| Mermaid                       | 2.7.5          |

---

## Session 1 — Arrive on the team: prepare a trustworthy workspace

### Prerequisites

- IBM Bob Version 2 installed on macOS, Linux, or Windows.
- IBM Bob Premium Package for Z 3.0.0 or later enabled.
- Zowe Explorer 3.5.0 or later and IBM Z Open Editor 6.6.0 or later installed.
- Git and internet access available.
- Mermaid and Draw.io integrations installed if you plan to complete the diagram session.

### User story

**As a new CBSA maintainer, I want a clean local workspace with clear rules for generated artifacts, so that I can investigate the application without creating documentation that the rest of the team cannot find or maintain.**

### Your task

1. Create a `CBSA` directory and open it in Bob.
2. Retrieve the lab source into the current workspace.
3. Ask Bob to establish artifact-location and naming rules before you begin analysis.

Use **Agent** mode for source retrieval. In a new task, enter:

```text
Retrieve the sub-directory named "src/base" in the CBSA directory from the GitHub repository https://github.com/ovallod/Bob4z-a-thon.git and place it in the current workspace folder. Then remove any temporary working directory you created.
```

Confirm that the workspace contains:

```text
base/
  bms_src/       CICS screens
  cobol_copy/    copybooks
  cobol_src/     COBOL programs
```

Next, in a new task, ask Bob to record the working conventions. You can write your own request, or use:

```text
Create workspace rules in .bob/rules or AGENTS.md:
- Store documentation in docs/. Store program-specific documentation in docs/<PROGRAM>/.
- Store tools in tools/.
- Store schemas, drawings, and graphs in graph/.
- Write documentation in English.

Use this filename pattern:
[PREFIX]-[TYPE]-[description].md

PREFIX is a program name, CBSA for application-level artifacts, or GLOBAL for cross-cutting artifacts.
TYPE is one of analysis, archi, docu, inv, plan, or spec.
Include examples in the rules.
```

If Bob offers to create a skill for this request, decline it; workspace rules are sufficient.

### Checkpoint

You have a local `base/` source tree and either `.bob/rules` or `AGENTS.md` explains where future deliverables belong and how they are named.

### Summary

You have established the team’s shared working agreement before generating artifacts. This small step makes later investigation reproducible and keeps the evidence from the lab usable after the session ends.

---

## Session 2 — Build the team’s map of the application

### Prerequisites

- Session 1 is complete.
- The `base/cobol_src/` sources are present in the workspace.
- You are using **Z Code** mode.

### User story

**As a maintainer assigned an unfamiliar incident, I want a reliable inventory and local metadata for CBSA, so that I can locate the programs, data structures, and dependencies relevant to the issue.**

### Your task

Initialize analysis metadata, generate a data dictionary, and create a component inventory.

In a new Z Code task, enter:

```text
/init and create the local metadata database with the scan_program tool.
```

Bob may inspect the workspace, scan programs, and update `AGENTS.md` with information that is not obvious from the file structure. When it offers a centralized metadata option, select local analysis.

Then start the **Generate data dictionary** workflow from the Play button. Select `BANKDATA.cbl` when asked for a source program. Bob should scan the COBOL directory if necessary, extract variables, and create or update `bobz/DD.json`.

Finally, request an inventory:

```text
Generate a complete inventory of the CBSA application. For each program, include its type, role, and dependencies: copybooks, BMS screens, Db2 tables and files with access mode, queues, and called programs. Save an exploitable Markdown document using the workspace conventions.
```

Optionally, capture existing code practices for later changes:

```text
/z-coding-standards-skill-builder
```

Accept inclusion of ZCodeScan if Bob asks. Test the resulting standards with:

```text
Verify whether @base/cobol_src/BNK1CAC.cbl follows the CBSA coding standards.
```

### Checkpoint

Verify that:

- local metadata is recorded in `.bobz/local-settings.json`;
- `bobz/DD.json` contains documented variables from `BANKDATA`;
- an inventory such as `docs/CBSA-INV-*.md` lists the key application components; and
- if you completed the optional step, a CBSA coding-standards skill exists under `.bob/skills/`.

### Summary

Instead of beginning with a guess about which program matters, you now have a searchable map of the estate. The metadata and dictionary make the rest of the lab’s questions more specific and easier to validate.

---

## Session 3 — Help a teller understand the account-inquiry journey

### Prerequisites

- Session 2 is complete.
- You have the inventory and local analysis metadata.
- Use **Z Code** for source analysis and **Ask** for a teller-facing explanation, if available.

### User story

**As a bank teller, I want a simple guide to viewing a customer’s accounts from the main menu, so that I can complete an inquiry confidently and explain common outcomes to a customer.**

### Your task

First identify the implementation path. Ask Bob:

```text
Explain the account-inquiry path that starts at BNKMENU and continues through BNK1CCA to INQACCCU. Identify the screens, programs, data accessed, and meaningful error paths.
```

Then create the teller guide. Use **Ask** mode if it is available:

```text
Create a user journey guide for a teller who consults a customer's accounts in CBSA.

The guide must:
- use non-technical language;
- begin at the main menu;
- describe each user decision and expected screen outcome;
- include screen examples or clearly labelled placeholders;
- include practical tips and a short troubleshooting section.

Save the guide using the workspace naming convention.
```

Review the result against the source-analysis explanation. The guide should describe what a teller does, while the implementation note explains why the screens behave that way.

### Checkpoint

You have a user-facing guide—typically named like `docs/CBSA-DOCU-account-inquiry.md`—and can trace its steps to `BNKMENU`, `BNK1CCA`, and `INQACCCU`.

### Summary

You translated implementation knowledge into a useful operational journey without losing traceability to the code. This is the foundation for distinguishing a user-reported problem from a technical cause.

---

## Session 4 — Explain the data initialization program to the team

### Prerequisites

- Sessions 1–2 are complete.
- `BANKDATA.cbl` is available at `base/cobol_src/BANKDATA.cbl`.
- Use **Z Code** mode.

### User story

**As a developer preparing a test environment, I want to understand how CBSA initializes banking data, so that I can use realistic data and avoid breaking assumptions made by online transactions.**

### Your task

Use the Play button to start **Explain code**, select `BANKDATA.cbl`, and select an audience perspective. Try more than one perspective if time allows: developer, architect, and business users need different information.

Alternatively, enter:

```text
/explain @base/cobol_src/BANKDATA.cbl
```

If the explanation appears only in the conversation, save it explicitly:

```text
Save the BANKDATA explanation as Markdown using the workspace naming convention.
```

Review the document for these questions:

- What data is generated or initialized?
- Which copybooks, VSAM files, Db2 tables, or supporting programs are involved?
- Which parameters or rules control the generated data?
- Which failure paths need attention before a test run?

### Checkpoint

A saved `BANKDATA` explanation exists under `docs/`, ideally in a `docs/BANKDATA/` directory, and addresses both the batch flow and its dependencies.

### Summary

You created a reviewable starting point for an important batch program. The explanation accelerates onboarding, but it still needs normal engineering validation before a production change or run.

---

## Session 5 — Find the rule hidden behind a customer-number validation

### Prerequisites

- Session 2 is complete.
- `base/cobol_src/BNK1CAC.cbl` is open in the editor.
- Use **Z Code** mode.

### User story

**As a product owner, I want new customer numbers to start with `99`, so that numbers created in the new channel can be recognized consistently by downstream processes.**

### Your task

Before changing code, ask Bob to extract the current rules:

```text
Extract the business rules from @base/cobol_src/BNK1CAC.cbl. Save them in a Markdown document using the workspace conventions. Separate customer-facing validation rules from technical implementation notes.
```

Review the customer-number rules. You should find checks for length, numeric content, and underscores. Locate the validation area around line 458, immediately after the numeric check.

In the editor, ask Bob to add the requested rule for the selected location:

```text
base/cobol_src/BNK1CAC.cbl:458-458

Add a validation that rejects a customer number unless it starts with 99. Use the established CBSA error-handling and message conventions.
```

Review the proposed edit before accepting it. It should:

- test the first two characters of the input customer number;
- show a clear error message;
- set the validation state and cursor location consistently with nearby checks; and
- be reflected in the generated business-rule document.

If the editor shows a missing `BNK1CAM` copybook, right-click the problem and choose **Correct with Bob**. Review the recreated copybook against the BMS definition. Do not treat unresolved CICS system copybooks, such as `DFAID`, as a reason to invent application copybooks.

### Checkpoint

The updated source and rule document both describe the new `99` prefix rule. You have reviewed the diff and can identify the existing validation convention the change follows.

### Summary

You used the existing code to discover the rule set before adding a new rule. This is safer than inserting an isolated condition because the new behavior now follows the program’s established validation and error-handling pattern.

---

## Session 6 — Determine whether CBSA can support multiple branches

### Prerequisites

- Session 2 is complete.
- The data dictionary and inventory are available.
- Use **Z Code** for variable exploration and **Z Architect** for impact analysis.

### User story

**As the architecture lead, I want to know the consequences of making `SORTCODE` variable rather than fixed, so that the business can decide whether and how to support multiple bank branches.**

### Your task

Build the evidence in stages. In Z Code mode, ask:

```text
What variables are used in INQACCCU?
```

Then focus the investigation:

```text
How is SORTCODE used across the CBSA application? Include occurrences, data access patterns, and implicit business rules.
```

In Z Architect mode, request the decision document:

```text
Analyze the impact of changing SORTCODE from a fixed value to a variable value to support multiple bank branches.

Include:
- current and target architecture;
- affected programs, copybooks, BMS maps, Db2 tables, VSAM files, and data access patterns;
- business rules that must be clarified;
- implementation sequencing, migration considerations, testing strategy, risks, and mitigations;
- clearly labelled assumptions and estimates.

Save the analysis as a Markdown artifact using the workspace conventions.
```

Optionally create a dependency view to support the review:

```text
Build a Draw.io architecture diagram that shows CBSA program dependencies, application layers, data stores, and major data flows.
```

### Checkpoint

You have an impact analysis such as `docs/CBSA-ARCHI-sortcode-impact.md`. It separates confirmed source evidence from assumptions, and it identifies the components and decisions that must be addressed before implementation.

### Summary

The team can now discuss the multi-branch proposal with concrete evidence instead of treating it as a local constant change. This is the moment to agree on business rules and migration ownership—not to start editing every affected program.

---

## Session 7 — Turn an email-search request into a safe delivery plan

### Prerequisites

- Sessions 2 and 6 are complete.
- You can use **Z Architect** and **Z Code** modes.
- The team has agreed that planning an email-search capability is in scope; do not apply changes to a production branch during this lab.

### User story

**As a teller, I want to find a customer by email address when I do not know the customer number, so that I can serve the customer without abandoning the inquiry.**

### Your task

First understand existing search capabilities:

```text
What criteria can be used to search for a customer in CBSA? Identify the programs, screens, data structures, and limitations involved.
```

Then use Z Architect to produce a plan:

```text
Create an implementation plan for customer search by email in CBSA.

Include requirements and non-goals, current-state evidence, data-model and access-path options, affected components, validation and privacy considerations, test scenarios, deployment steps, risks, and open decisions. Clearly distinguish facts found in the workspace from assumptions. Save the plan using the workspace conventions.
```

Review the plan with the team before coding. In particular, resolve:

- whether email is unique and whether partial matching is permitted;
- where email is stored and how existing records are migrated;
- the preferred Db2 or VSAM access path and performance expectations;
- validation, normalization, privacy, and audit requirements; and
- user-interface changes and error behavior.

Only after that review, use Z Code in a separate working branch or copy of the source to implement approved slices. For example:

```text
According to the approved email-search plan, propose the smallest first change to the CUSTOMER data structures. Show the affected copybooks, compatibility considerations, and tests before editing files.
```

For any generated COBOL, perform normal source review, syntax checking, compilation, and test execution in the appropriate z/OS environment. Do not consider an AI syntax review a compilation result.

### Checkpoint

You have a saved email-search plan—such as `docs/CBSA-PLAN-email-search.md`—that the team could use to make an implementation decision. If you started code changes, they are clearly separated from the plan and include a review/test checklist.

### Summary

You converted a broad feature request into a sequence of decisions and verifiable work. The deliverable is not merely code generation; it is a plan that makes design, risk, and testing visible before the team commits to the change.

---

## Lab wrap-up

You completed the work of a maintainer joining a legacy application:

1. prepared a governed workspace;
2. created metadata, a data dictionary, and an application inventory;
3. connected a teller journey to its implementation path;
4. documented a critical batch program;
5. discovered and safely extended a validation rule;
6. assessed a multi-branch architectural change; and
7. planned an email-search enhancement before coding it.

The artifacts you created are the real outcome of the lab: they make the codebase easier to navigate, review, explain, and change. Keep them current as the application evolves, and validate every proposed change through your normal engineering, security, and release processes.

## Suggested next step

Choose one approved, small change from the email-search plan. Create its technical specification, define acceptance and regression tests, then implement it in a controlled branch. Re-run the inventory or relevant documentation afterward so the project knowledge remains aligned with the source.
