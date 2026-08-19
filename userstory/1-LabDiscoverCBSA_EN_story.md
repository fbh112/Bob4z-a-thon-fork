# Lab: Onboarding a new developer into the CBSA application

## Discover, document, and change a CICS banking application with IBM Bob Premium Package for Z

**Estimated duration:** 4–6 hours   
**Level:** Intermediate   
**Audience:** Developers, Architecure and technical leads joining a mainframe application team  

---

## Lab story

It is your first week on the team. You have been given access to the **CICS Banking Sample Application (CBSA)** — a live banking system running on z/OS — and handed your first real assignment: the business wants to expand, and the application needs to support **multiple bank branches**.

Right now, CBSA is hardcoded to a single branch. Every transaction, every customer record, every account is tied to one fixed sort code. The ask sounds straightforward — make the sort code variable so different branches can operate independently. But before you write a single line of code, you need to understand what you are actually dealing with.

> **💡 What is a sort code?** A sort code is a unique identifier assigned to a specific branch of a bank. It is used to route transactions to the correct location, separate from the code that identifies the bank itself. Depending on the country, the same concept goes by different names — sort code (UK), routing number (US), IFSC code (India), or branch code elsewhere. In CBSA, it is stored as a fixed constant called `SORTCODE`.

The source code is there, but documentation is thin and the original developers have moved on. So you start with three basic questions:

- What does this system actually do?
- How do its pieces fit together?
- What breaks if the sort code changes?

That is exactly what this lab walks you through. Session by session, you will go from a blank workspace to a clear picture of the application — its programs, its data, its business rules, and the full impact s of the multi-branch change. Then, once the plan is approved, you will use IBM Bob to generate the code changes and implement the first slice of the feature.

No prior knowledge of CBSA is assumed. All you need is curiosity and the willingness to ask good questions.

---

## Learning outcomes

By the end of this lab, you will know how to:

- **find your way around** — set up a local workspace, scan the application, build an inventory, and locate what you need quickly;
- **read unfamiliar code** — use Bob to explain a COBOL program from developer and business perspectives;
- **trace a feature end to end** — follow a call chain from a teller screen down to the data layer and visualise it as a diagram;
- **map the blast radius** — find every occurrence of a hardcoded value across programs, copybooks, and data structures before proposing a change;
- **assess architectural impact** — turn raw evidence into a decision document that separates facts from assumptions;
- **plan before you code** — produce a phased implementation plan with acceptance criteria and a rollback strategy; and
- **implement with confidence** — use Bob to generate and apply the first code change, review the diff, and keep documentation in sync.

---

## Application background

### What the application does

**CBSA (CICS Banking Sample Application)** is a simulated bank-teller system running on IBM z/OS. Think of it as the kind of software a bank teller would use to look up customers, open accounts, and process transactions — built on mainframe technology that has been in use at real banks for decades.

> **💡 What is a bank teller?** A bank teller is a front-line bank employee who serves customers at a branch counter. Tellers handle everyday transactions — deposits, withdrawals, transfers, and account inquiries — and are often the first point of contact when a customer has a problem. In CBSA, the teller interacts with the system through a 3270 terminal screen.

A bank teller using CBSA can:

- create and look up customers and accounts;
- perform debits, credits, and fund transfers between accounts; and
- manage account details such as interest rates and account type.

### How it is built

CBSA follows a layered architecture common in mainframe applications:

| Layer              | Technology                | What it contains                                        |
|--------------------|---------------------------|---------------------------------------------------------|
| Presentation       | CICS + BMS (3270 screens) | The green-screen forms a teller sees and fills in       |
| Business logic     | COBOL programs            | The rules that validate input and drive each operation  |
| Data               | Db2 tables and VSAM files | Where customers, accounts, and transactions are stored  |
| Shared definitions | Copybooks                 | Shared data layouts reused across multiple programs     |

### Working in this lab

You are working from **local source files only** — no connection to a live z/OS system is required. IBM Bob analyses the source in your workspace and builds a local metadata database so you can navigate, query, and understand the application without needing a mainframe.

If Bob asks whether to use a backend metadata service such as Z Understand, choose **local workspace analysis** for this lab.

> **💡 Important to know — working with AI-assisted analysis**
>
> - **Non-determinism.** Bob uses AI, which is not deterministic. Results may differ slightly between runs, and you may need to rephrase a prompt to steer toward the expected outcome. This is normal — Bob will reach the result, but the path may vary.
> - **Counts and estimates.** AI-generated counts can be inaccurate. Treat them as starting points and validate through engineering review.
> - **Options and choices.** Bob may offer multiple options in response to a prompt. Choose the option that best fits the exercise goal. You can always restart a task and try a different option.

---

## Lab preparation

Before starting the lab, make sure the following are in place on your workstation:

- IBM Bob Version 2 installed (macOS, Linux, or Windows) — download from https://bob.ibm.com/download
- You have signed up for a Bob trial (sign up at https://bob.ibm.com/trial) and have access to Bob Premium Package for Z
- Git and Internet access available
- The following extensions installed in Bob:

| Extension                     | Tested version |
|-------------------------------|----------------|
| Zowe Explorer                 | 3.5.1          |
| IBM Z Open Editor             | 6.6.1          |
| IBM Bob Premium Package for Z | 3.0.10         |
| Mermaid                       | 2.7.5          |

---

## Session 1 — Arrive on the team: prepare a user workspace

### Prerequisites

- You are login to Bob 
- You are using **Z Code** mode

### Why

Think of this session as setting up your desk on day one. Before you dive into the code, you need three things in place: a workspace folder to keep everything together, the CBSA source code, and a quick briefing with Bob so it understands your project from the start.

### Your task

#### Step 1 — Create the workspace folder and open it in Bob

Create an empty directory on your machine called `CBSA` — this is your home base for everything you build in this lab. Then open it in Bob:

In Bob, choose **File → Open Folder** and select the `CBSA` directory.

You now have a clean, empty workspace. Everything Bob generates — diagrams, docs, analysis files — will live inside this folder.

#### Step 2 — Bring the source code locally

The CBSA source lives in a public GitHub repository. Pull it down and copy it into your workspace.

In Bob, choose **Terminal → New Terminal**, and type:

```bash
git clone https://github.com/ovallod/Bob4z-a-thon.git
cp -r Bob4z-a-thon/CBSA/src/base ./base
rm -rf Bob4z-a-thon
```

Take a quick look inside your workspace — you should see:

```text
base/
  bms_src/       ← CICS screens (what the bank teller sees)
  cobol_copy/    ← copybooks (shared data layouts)
  cobol_src/     ← COBOL programs (the business logic)
  README.md
```

Three folders, one banking application. You will get to know all of them.

#### Step 3 — Introduce your workspace to Bob

Before Bob starts generating anything, you want it to understand your workspace — where things live, what the project is called, and how output files should be named.

**First time using Bob chat?** Here is how to log in:

1. Open the Bob chat panel in the sidebar.
2. Click **Log in to Bob** — this opens the IBM Bob login page in your browser.
3. Enter your IBMid and complete authentication.
4. Once done, you will land back in the Bob chat window, ready to go.

In a new Bob task, switch to **Z Code** mode and type:

```text
/init
```

Bob will explore your workspace and create an `AGENTS.md` file — a living document that captures your project's structure and conventions. Open it and have a read. You will see Bob has already picked up the application layout from your source folders.

> **💡 Set up auto-approve**
> Tired of clicking *Approve once* every time? Select **Approve todo tools for task** to auto-approve all tool actions in the current task. You can also manage auto-approval permissions by clicking the **Permissions** tab at the bottom of the chat window.

### Checkpoint

Before moving on, make sure:

- your workspace has a `base/` directory containing `bms_src/`, `cobol_copy/`, and `cobol_src/`; and
- `AGENTS.md` exists in the workspace root and describes the application structure Bob discovered.

### Summary

Your workspace is ready. You have got the source, a clean folder structure, and Bob already knows the lay of the land. That `AGENTS.md` file might look small right now, but it is the foundation that keeps every generated artefact organised and findable as the lab grows.

> **💡 Meet your two mainframe modes**
>
> Bob Premium Package for Z gives you two specialised modes, and you will use both throughout this lab:
>
> | Mode | Best for | Examples |
> |---|---|---|
> | **Z Code** | *Understanding and changing code* | Reading programs, extracting business rules, building data dictionaries, writing or editing COBOL, generating technical docs |
> | **Z Architect** | *Understanding how the pieces fit together* | Architecture diagrams, impact analysis, dependency mapping, feasibility assessments, evolution planning |
>
> Quick rule of thumb: reach for **Z Code** when you are asking *"what does this do and how do I change it?"* — and **Z Architect** when you are asking *"what connects to what, and what breaks if I touch this?"*

---

## Session 2 — Get oriented: build the application map

### Prerequisites

- Session 1 is complete.
- The `base/` sources are present in the workspace.
- You are using **Z Code** mode.

### Why

You cannot judge risk in a system you have never seen. A five-minute inventory pass tells you the shape of the application — how many programs, how they are named, what is grouped together — before you commit to reading anything in depth. File names in this codebase are meaningful: `CREACC` = create account, `INQCUST` = inquire customer, `DBCRFUN` = debit/credit function. That alone tells you a lot.

### Your task

Build two artefacts that will serve as your reference throughout the rest of the lab: a program inventory and an architecture diagram.

**Inventory** — In **Z Code** mode, ask:

```text
I'm new to this codebase. Give me a directory-level overview of
base/cobol_src, base/cobol_copy, and base/bms_src — how many files
in each, and group the COBOL program names by what they seem to do
based on naming (e.g. inquiry, create, delete, transfer).

Save it as a markdown file under docs/
```

Alternative prompt: 
```text
Create a CBSA application inventory document with technical components, their uses, and main flows.
```

**Architecture diagram** — Switch to **Z Architect** mode and ask:

```text
Build an architecture diagram illustrating the program dependencies of the CBSA application.

Save it as a markdown file under docs/
```

### Checkpoint

- An inventory document such as `docs/overview.md` is saved and lists the key programs grouped by function.
- An architecture diagram document such as `docs/architecture.md` is saved and shows how the layers connect.

### Summary

You now have a searchable map of the entire application. Every session that follows builds on what you just created — the inventory tells you where to look, and the architecture diagram tells you what connects to what.

---

## Session 3 — Find ground zero: locate the hardcoded sort code

### Prerequisites

- Session 2 is complete.
- The inventory document is available in `docs/`.
- You are using **Z Code** mode.

### Why

Every hardcoded-value problem starts with finding ground zero. You want the *authoritative* definition, not just the first place you see the word "sortcode" in a search. In CBSA specifically, this step reveals something important: there is a copybook with the literal value, *and* a small CICS program built to return it — meaning someone already anticipated this might need to be looked up dynamically one day, but the rest of the system does not use that path yet. That is a real clue about design intent you would otherwise miss.

### Your task

- Search for where the sort code value is actually defined (not just referenced).
- Read that definition closely: what is its data type, what is its current value, is it a standalone item or part of something larger?
- Check whether there is already a "getter" program or utility that hands this value out, rather than every caller reading the literal directly.

In **Z Code** mode, ask:

```text
Search the codebase for where "sort code" or "sortcode" is defined
as a literal value (not just referenced). Show me the definition,
its data type, and its current value.

Then tell me if there's any "get" program for this value.
```

You will see the value is literally hardcoded in `base/cobol_copy/SORTCODE.cpy` with the value `987654`. You will also find a dedicated CICS program, `GETSCODE.cbl`, whose sole job is to return this value to the caller via a COMMAREA — but no other program currently calls it.

### Checkpoint

- You can name the file and line where `987654` is defined.
- You can describe what `GETSCODE.cbl` does and explain why no other program calls it yet.

### Summary

You found ground zero. The sort code lives in one copybook, every program copies it at compile time, and there is already a service program ready to return it dynamically — it just has no callers. That unused service is your solution. The next session will show you exactly how many programs need to change to wire it in.

---

## Session 4 — Assess the blast radius: impact analysis

### Prerequisites

- Session 3 is complete.
- You know where `SORTCODE.cpy` is defined and what `GETSCODE.cbl` does.
- Switch to **Z Architect** mode for this session.

### Why

Before proposing any change to the team, you need to answer two questions: *how much of the codebase does this touch?* and *what could go wrong?* An impact analysis turns those questions into a document your team lead can actually review — with named programs, named risks, and clear separation of what you know versus what you are assuming.

### Your task

In **Z Architect** mode, ask:

```text
Analyse the impact of changing SORTCODE from a fixed constant to a variable value
to support multiple bank branches.

Save the analysis as a markdown file under docs/
```

Bob will triger a build-in skill `impact-analysis` to evaluate the effort and risks and may ask you open questions to help you choose the best design strategy. Select the option that best fits the goal: enabling multi-branch support without changing any COMMAREA interfaces or recompiling programs on every branch change.

When the document is ready, share it with your team lead and talk through the open questions before proceeding. **Do not start coding until the assumptions are signed off.**

### Checkpoint

A saved impact analysis — such as `docs/impact-analysis-sortcode.md` — exists and:

- names every program that uses `SORTCODE` directly;
- identifies `GETSCODE` as the intended abstraction; and
- clearly separates confirmed evidence from open assumptions.

### Summary

You turned a vague business request into a concrete technical document with named risks and open decisions. The team can now have a real conversation about the multi-branch proposal. That is the job before any code moves.

---

## Session 5 — Write the implementation plan

### Prerequisites

- Session 4 is complete and the impact analysis has been reviewed by your team lead.
- The open questions from the impact analysis have been answered.
- You are using **Z Architect** mode.

### Why

An impact analysis tells you *what* is affected. An implementation plan tells you *how* and *in what order* to make the change safely. A good plan also includes a rollback step for each phase — so if something goes wrong in production you know exactly how to undo it.

### Your task

In **Z Architect** mode, ask:

```text
Based on the SORTCODE impact analysis in the workspace, create a phased implementation
plan for the multi-branch feature.

Separate each phase clearly. Save the plan using the workspace conventions.
```

Bob will triger a build-in skill `implementation-planning` to produce a phased plan. Review it with your team and pay special attention to:

- **the sequence** — does each step leave the application in a working state?
- **the test scenarios** — are there cases that only appear when two branches are active simultaneously?
- **the rollback plan** — could you safely undo Phase 1 if something went wrong in production?

Only move to Session 6 once the plan is reviewed and approved by your team lead.

### Checkpoint

A saved implementation plan — such as `docs/implementation-plan-sortcode-multibranch.md` — exists, is phased, and includes acceptance criteria and a rollback step for each phase.

### Summary

You have a plan, not just a wish list. The plan is what you show your team lead for approval. It is also what you will use in the next session to guide Bob's code generation — because a well-specified plan produces much better code than a vague prompt.

---

## Session 6 — Implement the first slice

### Prerequisites

- Session 5 is complete and the implementation plan is approved by your team lead.
- You are using **Z Code** mode.

### Why

Good implementation starts with the smallest change that moves the system in the right direction. Rather than changing all programs at once, Phase 1 of the plan targets the most complex business logic program first — `CREACC.cbl` — and uses it as a proven template for the remaining programs. But before you change a single line, you document what the program currently does. That baseline is your safety net.

### Your task

#### Step 1 — Document `CREACC.cbl` before touching it

Before you change a single line of code, document the program you are about to modify. Why document first? because it is one of the most important engineering habits on a mainframe team. 

In **Z Code** mode, use the "Start Workflow" icon, select **Generate program documentation**, click `Start`, and select `base/cobol_src/CREACC.cbl`. Alternatively, type in the chat:

```text
Generate documentation for @base/cobol_src/CREACC.cbl

Save it to docs/
```

Bob will produce a structured document covering:

- what the program does (its business function)
- the key paragraphs and their execution sequence
- the data stores it reads and writes (Db2 `ACCOUNT`, `PROCTRAN`, and `CONTROL` tables; Named Counter Service)
- the programs it calls (`INQCUST`, `INQACCCU`, `ABNDPROC`)
- the error and abend paths

Read the document before continuing. Pay particular attention to:

- the **entry sequence in `P010`** — this is where you will insert the `GETSCODE` call
- the **Named Counter sections (`ENC010`, `DNC010`)** — the sort code is part of the counter name, so your change directly affects counter identity
- the **Db2 write sections (`WAD010`, `WPD010`)** — the sort code is written into the `ACCOUNT` and `PROCTRAN` rows, which is the whole reason this change matters for multi-branch

> **💡 Why `CREACC.cbl` first?**
> The implementation plan starts with `CREACC.cbl` because it is the most complex program in Phase 1 — it uses `SORTCODE` in **six paragraphs** (`P010`, `ENC010`, `DNC010`, `FNA010`, `WAD010`, `WPD010`) and will serve as the template all other programs follow. If the pattern works here, it works everywhere. If there is a design issue, you want to find it on program one, not program ten.

#### Step 2 — (Optional) Establish CBSA coding standards

Before generating any new code, it is good practice to discover and formalise the coding standards already in use in the workspace. This ensures Bob's generated code matches your team's existing style.

In a new Bob task in **Z Code** mode, type:

```text
/z-coding-standards-skill-builder
```

This runs the built-in `z-coding-standards-skill-builder` skill, which analyses the codebase and creates a reusable `.bob/skills/cbsa-coding-standards` skill. If asked about ZCodeScan validation, select **Yes**.

To test the new skill, start a new task and type:

```text
Verify @base/cobol_src/CREACC.cbl is respecting CBSA coding standards
```

Bob will use the newly created skill to review the program and report any deviations.

#### Step 3 — Propose the Phase 1 change

Now ask Bob to propose the change before applying it. Reviewing a proposal first gives you the chance to catch anything unexpected before any file is edited.

In **Z Code** mode, type:

```text
According to the approved multi-branch implementation plan, propose the Phase 1 change.

Do not edit files yet — show me the proposal first.
```

Review the proposal carefully:

- Does it remove `COPY SORTCODE.` from Working-Storage?
- Does it add a `WS-GETSCODE-COMMAREA` and call `EXEC CICS LINK PROGRAM('GETSCODE')` at the start of `P010`?
- Does it replace every bare `SORTCODE` reference in the Procedure Division with `WS-SORTCODE`?
- Does the abend handling on the new `EXEC CICS LINK` follow the standard CBSA pattern?

#### Step 4 — Apply the change and review the diff

When you are satisfied with the proposal, ask Bob to apply it:

```text
Apply the Phase 1 changes to @base/cobol_src/CREACC.cbl following the CBSA coding standards.
```

After Bob applies the changes, review the diff carefully — compare it against the documentation you generated in Step 1 and confirm that:

- the entry sequence documented in `P010` is preserved, with the new `GETSCODE` call placed at the very start
- every paragraph that previously used the bare `SORTCODE` identifier now uses `WS-SORTCODE`
- the Named Counter name still incorporates the sort code (now retrieved dynamically)
- the Db2 writes to `ACCOUNT` and `PROCTRAN` now use the runtime value from `WS-SORTCODE`

#### Step 5 — Keep the documentation in sync

After every code change, update the inventory and architecture documents so the team's knowledge base reflects the new state.

```text
Update the existing inventory documentation and relevant documentation to reflect the changes.
```

> **💡 Important reminder**
> Bob's generated COBOL is a proposal, not a finished product. Before any code reaches a real z/OS system, it must go through your team's normal review, syntax checking, compilation, and test execution. AI-assisted code generation is a starting point — it is not a substitute for a compiler.

### Checkpoint

Before marking this session complete, verify:

- A documentation file for `CREACC.cbl` exists under `docs/` and was generated **before** any code was changed.
- (Optional) The skill `.bob/skills/cbsa-coding-standards` exists in the workspace.
- `base/cobol_src/CREACC.cbl` has been updated: `COPY SORTCODE.` removed, `EXEC CICS LINK PROGRAM('GETSCODE')` added at `P010`, and all `SORTCODE` references in the Procedure Division replaced with `WS-SORTCODE`.
- The inventory and architecture documents in `docs/` reflect the updated state.

### Summary

You made the first real step toward multi-branch support — and you did it the right way: **document first, then change, then keep records in sync**. The documentation gave you a baseline before you touched anything, made the scope of the change obvious, and gave your reviewer something concrete to work with. `CREACC.cbl` is now the proven template — every other program in Phase 1 follows the same four-step pattern.

---

## Lab wrap-up

You have just done what a real developer does when they join a team with a live mainframe application and a demanding feature request:

1. **Set up a governed workspace** — so your work is findable and reproducible.
2. **Built an application map** — inventory, architecture diagram, metadata database.
3. **Found ground zero** — the authoritative definition of the hardcoded value and the unused service already designed to replace it.
4. **Assessed the blast radius** — a documented impact analysis your team lead could actually review, with named risks and open assumptions.
5. **Wrote an implementation plan** — phased, sequenced, with acceptance criteria and a rollback step.
6. **Documented before changing** — a pre-change baseline for the first program.
7. **Made the first safe code change** — smallest slice, proposal reviewed before applying.
8. **Kept documentation in sync** — the inventory and architecture docs reflect the new state.

The habit behind all of this is simple: **understand before you change, and plan before you code**. The tools change. The frameworks change. That habit stays.

The artefacts you created are not throwaway — they are the living documentation of the application. Keep them updated as the work continues.
