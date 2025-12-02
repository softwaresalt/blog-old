# **Just Start Building: A Guide to Iterative Context-Driven Development (ICDD)**

The Core Philosophy: Process Over Platform  
This methodology is grounded in Kasparov's Law:  
*"A weak human \+ a machine \+ a better process beats a strong human \+ a machine \+ an inferior process."*

We believe that a better development process isn't about buying a new tool, switching IDEs, or installing a complex CLI framework. It is about aligning how humans naturally think with how AI naturally works.

Humans work best through interactive exploration—we discover requirements as we build.  
AI agents work best through rich context—they need a persistent memory of constraints and goals.  
**Iterative Context-Driven Development (ICDD)** is the bridge between the two. It is a lightweight, human-centered methodology designed to be completely agnostic to your toolset. Whether you use Jira or Trello, VS Code or Neovim, Copilot or Claude—this process works because it relies on the universal language of software: **Text files in Git.**

## **Part 1: The Three Core Principles**

### **1\. Radical Simplicity**

The process must be simpler than the problem it solves. If you struggle to get your environment properly set up just to start coding, the process has failed. ICDD requires only Markdown files and natural language.

### **2\. Intuitive Exploration**

Humans rarely know the perfect architecture before they write the first line of code. Rigid specs fail because they don't allow for discovery and require too much up-front analysis and planning. ICDD treats the "Plan" as a living map that you can co-create with an AI agent during the initial planning phase. As you explore and develop the code and hit roadblocks, you update the map. The documentation evolves *with* your understanding, not *after* the fact. This includes the system context, since the full knowledge of how a complex system works is rarely fully documented with much of it only existing as tribal knowledge. As this knowledge is discovered, it can and should be iteratively codified in the system context.

### **3\. Absolute Agnosticism**

Your process should not break because you changed companies or switched laptops.

* **IDE Agnostic:** It works in Cursor, JetBrains, VS Code, or Vim.  
* **Agent Agnostic:** It works with any LLM that can read text.  
* **Workflow Agnostic:** It integrates with Jira, GitHub Issues, or Azure DevOps without requiring a plugin—just a simple naming convention.

## **Part 2: Descriptive, Not Prescriptive (The Flexibility Rule)**

This methodology is a conceptual framework, not a cage. It is **descriptive** (describing a way to work) rather than **prescriptive** (dictating exactly how you must work).

Every organization has unique constraints, regulatory requirements, and team structures. ICDD is designed to be adapted to your working environment, rather than forcing you to redefine your environment to fit the process.

**Examples of Adaptation:**

* **Scaling Context (File vs. Folder):**  
  * *Small Team:* A single .context/system\_context.md file is efficient and easy to maintain.  
  * *Enterprise:* For a massive system, a single file might be too large. You can replace the file with a folder (.context/system/) containing specific context files like security\_protocols.md, legacy\_integration.md, and user\_personas.md.  
* **Backlog Organization:**  
  * *Agile:* You might group your .context/backlog.md by "High Priority" vs. "Icebox."  
  * *Release-Driven:* You might structure sections by Product Version (\#\# v2.1 Release, \#\# v2.2 Release) to better support forward planning and roadmapping.  
* **Unit of Work:**  
  * Your "Feature Context Files" do not strictly have to be "features." They can represent Epics, User Stories, Bug Fixes, or Technical Debt Cleanup. Use whatever grouping aligns with your existing ticketing system (Jira, Azure, etc.).

**The Bottom Line:** Adopt the *principles*, but adapt the *artifacts* to fit your scale.

## **Part 3: Setting Up Your Repo**

You do not need plugins. You just need a structured place to store your "Shared Brain."

### **1\. The Directory Structure**

In your existing Git repository, create a dedicated folder for your context. We recommend .context because it self-describes intent.

**Naming Conventions:** To maintain workflow agnosticism while enabling integration, we recommend prefixing files with your **Issue Tracker ID** (if you use one).

my-project/  
├── .context/              \<-- The "Shared Brain"  
│   ├── system\_context.md  \<-- GLOBAL TRUTHS (Or a folder for complex systems)  
│   ├── backlog.md         \<-- The Dynamic Backlog (Idea Bin)  
│   ├── active/            \<-- Features currently being built  
│   │   └── PROJ-101-user-auth.md  \<-- Prefixed with Jira/GitHub ID  
│   ├── archive/           \<-- Completed features (for reference)  
│   └── adr/               \<-- Architectural Decision Records (The Anchors)  
│       └── PROJ-101-db-choice.md  \<-- Linked to the ticket that forced the decision  
├── src/  
├── package.json  
└── README.md

Why match the IDs?  
By matching your filename (PROJ-101...) to your DevOps ticket, you prepare your repo for Model Context Protocol (MCP) integrations. An AI agent with MCP access can read the file ID, automatically fetch the corresponding requirements from Jira/Azure, and even update the ticket status when you finish—bridging the gap between code and management.

### **2\. The Golden Rule**

**Never start coding without a Context File.** Before you write a line of code, you must anchor your work in the existing system context and define the specific problem in a feature file.

## **Part 4: The Artifacts (Templates)**

ICDD relies on four simple Markdown artifacts.

### **Artifact A: The System Context (Global)**

Filepath: .context/system\_context.md (or .context/system/\*.md)  
The "Constitution" of your project.  
\# System Context: \[Project Name\]

\#\# 1\. The Users & Stakeholders  
\* \*\*Primary User:\*\* \[e.g., Senior Data Analyst\] who values precision over speed.  
\* \*\*Stakeholder:\*\* \[e.g., Security Team\] requires all PII to be encrypted at rest.  
\* \*\*Stakeholder:\*\* \[e.g., Marketing\] requires all UI components to match the Design System.

\#\# 2\. The Technology Stack  
\* \*\*Language:\*\* TypeScript 5.0+  
\* \*\*Framework:\*\* React 18 (Next.js App Router)  
\* \*\*Database:\*\* PostgreSQL via Supabase

\#\# 3\. Global Constraints  
\* No new external dependencies without approval.  
\* All code must pass strict linting rules defined in \`.eslintrc\`.

Recommendation: The "Text-First" Standard  
Organizations often produce visual artifacts like architecture diagrams, extensive slide decks, or whiteboard sketches. While these are excellent for human-to-human communication, they are often lossy or opaque to AI agents.  
**Transcribe your visuals.** Do not solely rely on linking to an image of a diagram. Instead, write a clear, text-based exposition of the visual (e.g., "The User Service sends a sync request to the Auth Service, which then validates the token against Redis..."). This ensures your system context is fully consumable by the model, capturing the *intent* of the diagram rather than just its pixels.

### **Artifact B: The Backlog**

Filepath: .context/backlog.md  
A low-friction list of ideas.  
\# Dynamic Backlog

\#\# v2.1 Release (Current Sprint)  
\- \[ \] \*\*PROJ-123:\*\* Implement Magic Links (replaces password login).  
\- \[ \] \*\*PROJ-124:\*\* Add "Export to CSV" for the finance team.

\#\# v2.2 Release (Planning)  
\- \[ \] Dark mode support?

### **Artifact C: The Feature Context File (Local)**

Filepath: .context/active/\[TICKET-ID\]-\[feature-name\].md  
The living workspace for a specific unit of work.  
\# Feature: \[Ticket ID\] \- \[Feature Name\]  
\*\*Status:\*\* In Progress  
\*\*Owner:\*\* \[Your Name\]  
\*\*Related Ticket:\*\* \[Link to Jira/GitHub/Azure Issue\]

\#\# 1\. The Vision  
\*In plain English, describe what we are building and why.\*

\#\# 2\. Constraints & "Must-Haves"  
\* Must align with \`.context/system\_context.md\`.  
\* Must match existing UI styling.

\#\# 3\. The Plan (Living Document)  
\*Initialize this as a high-level list.\*  
\- \[ \] Step 1: \[TBD\]  
\- \[ \] Step 2: \[TBD\]

\#\# 4\. Current State / Scratchpad  
\*Paste error logs or temporary JSON structures here.\*

### **Artifact D: The Anchor (ADR)**

Filepath: .context/adr/\[TICKET-ID\]-\[title\].md  
For major architectural decisions. Using the ticket ID here helps future developers understand why a decision was made at that specific moment in time.  
\# ADR-\[Ticket ID\]: \[Title\]  
\*\*Date:\*\* \[YYYY-MM-DD\]  
\*\*Status:\*\* Accepted

\#\# Context  
We needed to choose between A and B because...

\#\# Decision  
We chose \[Option A\].

\#\# Consequences  
\* \*\*Positive:\*\* Faster development time.  
\* \*\*Negative:\*\* Higher memory usage.

## **Part 5: The Six Key Personas**

To maximize the effectiveness of your AI, explicitly invoke a specific "Persona" at each stage of the process. This focuses the AI's attention on specific concerns (security, usability, architecture) rather than general code generation.

1. **The Planner (Product Owner):** Focuses on user value, requirements, and "The Vision." Doesn't care about code details, only outcomes.  
2. **The Architect:** Focuses on system design, database schemas, and integration with system\_context.md.  
3. **The Codex (Engineer):** The builder. Focuses on clean syntax, efficient logic, and adhering to the tech stack.  
4. **The Tester:** Skeptical. Looks for edge cases, missing error handling, and logical gaps.  
5. **The Reviewer:** Critical. Checks for style violations, best practices, and potential refactoring needs.  
6. **The Security Engineer:** Paranoid. Checks for injections, auth bypasses, and PII handling.

## **Part 6: The Workflow (The Loop)**

This loop mirrors the human tendency to explore. We do not plan everything upfront; we plan enough to start, and we refine the plan as we discover the reality of the code.

### **Step 1: Selection & Setup**

1. Pick an item from .context/backlog.md.  
2. Create .context/active/PROJ-123-auth-flow.md (Match the ID\!).  
3. Fill in the **Vision** and copy relevant constraints from **System Context**.

### **Step 2: The Handshake (Planning)**

**Your Prompt:**

"Act as a Planner and Architect.  
Read .context/system\_context.md (for global constraints) and .context/active/PROJ-123-auth-flow.md (for specific requirements).  
Propose a 3-step technical implementation plan. Do not write code yet. Just update the 'Plan' section of the feature file."

### **Step 3: The Build Loop (Exploration & Execution)**

Execute the plan step-by-step.

**Your Prompt:**

"Act as a Codex (Engineer).  
Reference .context/active/PROJ-123-auth-flow.md. Implement Step 1 of the plan.  
Adhere strictly to the Tech Stack defined in the System Context."

**Crucial Logic (The Exploration):** Humans discover things while coding. If you find a library is outdated or a pattern doesn't work, **STOP.**

1. Go back to your Feature Context File.  
2. Update the "Plan" section to reflect the new reality.  
3. **Prompt:** "Act as a **Planner**. I updated the context file because \[Library X\] is deprecated. Read the update and propose a new path forward."

### **Step 4: Verification & Anchor**

Before closing the ticket, run a safety check.

**Your Prompt:**

"Act as a Security Engineer and Code Reviewer.  
Review the code we just wrote for the Auth Flow. Are there any vulnerabilities or style violations based on our system\_context.md? Suggest fixes."

Once clear, create an ADR if needed, move the file to archive/, and update the backlog.md.

## **Part 7: Inspiration & Tool Alignment**

ICDD is an evolution of the contribution of others. It synthesizes the best parts of rigid frameworks and flexible tools into a human-centric workflow.

### **The Backlog.md CLI Toolset**

The **Backlog.md CLI** is a powerful toolset that championed the idea of keeping project management directly in your repo. While it generates a readable backlog.md file for the team, it typically relies on a strict JSON data store (.backlog/ folder) to manage the actual state and history of tasks.

* **Alignment:** ICDD fully embraces the core philosophy of this ecosystem: that your backlog belongs in version control, next to your code, not in a separate SaaS silo.  
* **The Difference:** The CLI toolset provides a robust, command-line interface for managing tasks. ICDD adopts the *format* (a markdown backlog) but remains agnostic to the *tool*. You can use the Backlog.md CLI if you prefer structured commands, or you can simply edit the file by hand (or ask your AI agent to "Move item X to done"). The process works either way.

### **GitHub Copilot & Chat Modes**

GitHub Copilot has introduced features like "Chat Modes," which allow developers to define explicit personas via Markdown and configure specific MCP tools for them.

* **Alignment:** ICDD's "Six Key Personas" (Part 5\) can be directly implemented using these artifacts. You can formalize the "Planner" or "Security Engineer" by creating specific persona files with detailed system prompts.  
* **The Difference:** While Copilot provides the *mechanism* to define a persona, ICDD provides the *process* for when and how to use them.

### **Spec-Kit & The "Constitution"**

Tools like **Spec-Kit** pioneered the use of highly structured Markdown files and specific "Slash Commands" (like /plan, /execute, /test) to guide AI through development phases. It also introduced a Constitution.md, serving the same purpose as our system\_context.md.

* **Alignment:** Both methodologies agree that AI needs a "Single Source of Truth" regarding constraints and stakeholders.  
* **The Extension (Personas as Flexible Commands):** ICDD applies the logic of slash commands but extends them into **Personas**. For example, Spec-Kit's /plan becomes the **Planner/Architect** persona; /execute becomes the **Codex**; and /test becomes the **Tester**. By using Personas instead of rigid commands, you maintain the discipline of specialized roles while gaining the flexibility to have a nuanced, back-and-forth conversation with that role—something a CLI command cannot easily support.  
* **The Difference:** Spec-Kit is often "up-front heavy," relying on the plan to run through an entire AI-driven development cycle. ICDD rejects the idea that you can plan perfectly before you build. We use the context files to guide **interactive** development, allowing the human to explore and pivot as they code, rather than waiting for an automated script to finish.

## **Part 8: Methodology Comparison**

Why choose ICDD? Because it prioritizes human cognitive flow over rigid tooling.

| Feature | Frameworks (e.g., Spec-Kit, Amplifier, Backlog.md) | Standard Chat (ChatGPT, Copilot without context) | ICDD Methodology |
| :---- | :---- | :---- | :---- |
| **Philosophy** | "The Tool defines the Process." | "Speed over Structure." | **"Human Context defines the Code."** |
| **Cognitive Load** | **High.** Must learn CLI commands and config schemas. | **High.** Must constantly remind AI of constraints. | **Low.** Constraints are offloaded to text files. |
| **Process Friction** | **High.** Rigid steps that block exploration. | **Low.** But prone to errors and amnesia. | **Fluid.** Supports iterative discovery. |
| **Tool Dependency** | **High.** Locked into specific ecosystems. | **High.** Dependent on one model/interface. | **None.** Works in any editor, with any AI. |
| **Stakeholder Visibility** | **Variable / Tool Dependent.**\[^1\] | Non-existent. | **Front-and-center in system\_context.md.** |

### **The Bottom Line**

Complexity is the enemy of execution. You do not need a smarter tool; you need a process that respects how you think. ICDD allows you to explore, iterate, and build complex software without becoming a slave to your tools.

\[^1\]:  
Nuance on Visibility: Tools like Backlog.md CLI and Spec-Kit use accessible Markdown (e.g., backlog.md, Constitution.md), similar to ICDD. However, "Heavy Frameworks" often couple these readable files with strict syntax, metadata headers, or CLI-specific requirements that can make them feel more like configuration inputs for an automation pipeline rather than pure communication documents for the team.