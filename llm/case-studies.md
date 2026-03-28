Context for continuation
I am documenting a series of case studies on using GitHub Copilot as an agentic LLM driver to accelerate development on a Rates trading platform. The audience is fellow developers and tech leads. The goal is to convince the team to formally adopt shared skills and workflows.
The four case studies are:

Skill-Driven Code Review — Using a skills .md file (Rules of Hooks) to drive PR reviews via Copilot, consistently surfacing React hook misuse and suggesting idiomatic refactors.
Architectural Exploration & Directed Build — Using the agent to compare two parser approaches (recursive descent vs LL* parsing) for a Bloomberg financial strategy string format, then directing the build with clear separation of concerns: grammar definition → CST → projection. Delivered in two weeks vs an estimated one to two months.
The Checkpoint Loop: Test Migration — A stateful, resumable agentic workflow for migrating legacy Playwright page objects to new standards. Uses a PRD task file and a progress.txt log. Each Copilot session picks the next task, executes it, logs it, marks it complete, and stops. A fresh session resumes from the next task.
Audit to Delivery: Fake Services & UI — The same checkpoint loop pattern applied to aligning fake RFQ services (client RFQ and sales RFS) with real service behaviour, plus generating a features panel UI. Completed in half a day, with the agent working autonomously between meetings.

Document format: Four separate case study documents, each with: pattern summary box, context & problem, execution steps, prompt templates, code samples (style varies per case study), outcome table (estimated vs actual effort), and reusable takeaway.
Agreed terminology: The task-loop pattern is called The Checkpoint Loop. Code sample style is a mix depending on the case study.
Next step: Draft Case Study 1 in full.