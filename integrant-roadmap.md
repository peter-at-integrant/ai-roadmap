# Agency AI Adoption Plan

**Goal:** AI Adoption
**Created:** 01/26/2026
**Status:** Draft

---

## Overview

This plan outlines a comprehensive AI adoption strategy for Agency, focusing on automating the Software Development Lifecycle (SDLC) and progressively advancing through four phases of AI integration, each corresponding to a technical level.

---

## Phase 1: Level 0 — Maximizing AI Tool Usage

### Objective

Automate the entire Software Development Lifecycle using AI tools in a generic, tool-agnostic way.

> **On timelines:** The "Target: Week X" deadlines represent when the team member should have
> completed learning and passed the self-assessment for that goal — not when the artefacts were
> built. AI compresses implementation time to hours; the week budget is for guided exploration,
> experimentation, pair-prompting with AI, and demonstrating understanding to the EM/TL.

### Process Flow

| Step | Description |
|---|---|
| Requirement Gathering | Automated collection and analysis of requirements |
| Work Item Creation | Convert requirements into structured work items |
| Implementation | Automated code generation and implementation |
| Pull Request Creation | Automated PR generation |
| Testing | Automated test creation and execution |
| Deployment | Automated deployment processes |
| Project Management | Project oversight and coordination |
| Business and Technical Documentation | Documentation as part of Definition of Done (DoD) |
| Team Metrics and Reports | Automated metrics collection and reporting |

### Roles Impacted

| Role | Automation Focus |
|---|---|
| Product Owners (POs) | Requirement gathering automation |
| Developers (Devs) | Implementation automation |
| SDET | Testing automation |
| DevOps | Deployment automation |
| Technical Project Leads (TPLs) | Overall process orchestration |
| Data | Data-related automation and processing |

---

## Level 0: Maximizing AI Tool Usage

**Goal:** Use AI tools the right way to fully automate SDLC in a generic way that works with any tool
*(Cursor, Claude Code, Antigravity, etc.)*

### Training Outline

1. **Multi-Repository Root Setup** — Configure a root repository structure that enables AI tools to access and coordinate changes across all team repositories, allowing simultaneous feature implementation across multiple codebases
2. **AGENTS.md as Tool-Agnostic Source of Truth** — Establish AGENTS.md as the central repository for team and project guidelines, ensuring AI tools can access standardized practices without being tied to any specific tool
3. **Automated Work Item Creation** — Automate work item creation from requirements documents following standardized templates and document them into the knowledge base (KB)
4. **Project Architecture and Standards Context** — Keep AI tools informed about project architecture, code standards, different projects and their usage, business context, and tech stack to reduce token consumption and produce unified, high-quality code
5. **Business Documentation Automation via MCPs** — Automate business documentation generation using Model Context Protocol (MCP) servers to streamline documentation workflows
6. **Automated Branching and PR Creation** — Automate branch creation and pull request generation across multiple repositories with consistent standards and workflows
7. **Automated Testing with Playwright MCP** — Implement automated test case generation and execution using Playwright with MCP integration
8. **Skills-Based Guideline Encapsulation** — Package team instructions and guidelines as reusable skills using OpenSkills, enabling AI tools to install and reference standardized practices

---

## Phase 1 Training Outline

Training content for teams to effectively leverage AI tools and automation in Phase 1:

### 1. Multi-Repository Root Setup
- Configure a root repository structure that enables AI tools to access and coordinate changes across all team repositories
- Enable simultaneous feature implementation across multiple codebases
- Establish cross-repository code change capabilities
- Coordinate and synchronize changes across multiple repositories

### 2. AGENTS.md as Tool-Agnostic Source of Truth
- Establish AGENTS.md as the central repository for team and project guidelines
- Structure AGENTS.md for optimal AI tool understanding and accessibility
- Maintain AGENTS.md as the single source of truth for all team standards and practices
- Ensure a tool-agnostic approach that works with any AI tool or platform

### 3. Automated Work Item Creation
- Automate work item creation from requirements documents following standardized templates
- Extract requirements from source documents and convert them into structured work items
- Apply team-defined templates to ensure consistency across all work items
- Document generated work items into the knowledge base (KB) for traceability and reference
- Integrate work item creation into the requirements-to-implementation workflow
- Ensure all work items include necessary context for AI-assisted development

### 4. Project Architecture and Standards Context
- Keep AI tools informed about project architecture, code standards, different projects and their usage, business context, and tech stack
- Enable AI tools to follow the same code standards and architecture of the project
- Maintain comprehensive project context documentation accessible to AI tools
- Document different projects, their purposes, and specific usage patterns
- Provide business context and technical stack information to reduce token consumption
- Ensure AI-generated code adheres to project-specific architecture patterns and coding standards
- Create unified, high-quality code output by keeping AI tools consistently informed
- Reduce redundant context queries by maintaining accessible project knowledge base

### 5. Business Documentation Automation via MCPs
- Automate business documentation generation using Model Context Protocol (MCP) servers
- Create and configure MCPs specifically for documentation automation workflows
- Integrate documentation requirements seamlessly into the development workflow
- Ensure documentation completion is part of the Definition of Done (DoD)

### 6. Automated Branching and PR Creation
- Automate branch creation and pull request generation across multiple repositories
- Implement automated branch creation following team naming conventions and standards
- Generate pull requests automatically with consistent formatting and required information
- Ensure all PRs adhere to team standards, guidelines, and review requirements
- Coordinate PR creation workflows across multiple repositories simultaneously

### 7. Automated Testing with Playwright MCP
- Implement automated test case generation and execution using Playwright with MCP integration
- Apply Playwright for end-to-end and integration testing automation
- Create and configure MCPs specifically for Playwright test automation workflows
- Auto-generate test cases based on feature requirements and specifications
- Integrate automated test creation and execution seamlessly into the development process
- Ensure comprehensive test coverage as part of the automated workflow

### 8. Skills-Based Guideline Encapsulation
- **Skill Creation:** Learn how to create skills that encapsulate team guidelines and best practices
- **OpenSkills Usage:** Use OpenSkills to manage and load skills on-demand for AI tools
- **Guideline Encapsulation:** Convert AGENTS.md content and team guidelines into reusable, portable skills
- **Skill Installation:** Install skills so they can be read and referenced by AI tools
- **Skill Structure:** Understand skill format and organization for optimal AI tool integration
- **Skill Loading:** Use `npx openskills read <skill-name>` to load specific workflows and guidelines when needed
- **Custom Skills:** Create project-specific skills for:
  - Development coding standards
  - PR review processes
  - Architecture patterns
  - Work item templates
  - Testing standards
  - Documentation requirements
- **Skill Management:** Organize and maintain skills for team-wide adoption and consistency
- **Integration:** Integrate skills into AI tool workflows to ensure consistent guideline application across all tools

### Training Focus Areas

| Area | Description |
|---|---|
| Context Management | How to structure AGENTS.md and documentation for optimal AI understanding |
| Skill Development | Creating reusable Anthropic skills to encapsulate guidelines and best practices |
| Workflow Integration | Seamlessly integrating AI tools into existing development workflows |
| Quality Assurance | Ensuring AI-generated code and PRs meet team standards |
| Tool Configuration | Setting up MCP servers, multi-repo access, and OpenSkills for effective AI assistance |

---

## Phase 2: Level 1 — AI Integration Engineer

### Objective

Develop practical AI fundamentals and system integration capabilities.

### Focus

Practical AI Fundamentals & System Integration

### Key Components

- Understand more about LLMs, how they differ from agents
- Create MCPs
- Explore leading open-source LLMs (e.g., Mistral, LLaMA, Falcon) and learn to run them locally via Ollama and LM Studio
- Learn the role of vector databases (e.g., Chroma) and embeddings in AI apps
- Implement Retrieval-Augmented Generation (RAG), Context-Aware Generation (CAG), and emerging concepts like Graph-RAG for structured knowledge augmentation
- Automate workflows using tools like n8n or Airflow, connecting AI with business operations

---

## Phase 3: Level 2 — AI Agent Engineer

### Objective

Build autonomous AI agents and workflow automation capabilities.

### Focus

Autonomous AI Agents & Workflow Automation

### Key Components

- Learn Python
- Create Complex MCPs
- Learn agent orchestration frameworks like Langchain, LlamaIndex, AutoGen, and CrewAI
- Build autonomous multi-step agents that can reason, act, and interact across tools and APIs
- Cost analysis of the solutions (to know what is the cost of hosting your solution either locally or on cloud)
- Security awareness of LLMs usage (to know the criticality of data exposure to cloud based LLMs)
- Cloud based Agents services and SDKs (e.g. AWS Bedrock, Azure AI Foundry)

---

## Phase 4: Level 3 — AI Architect

### Objective

Design and scale AI systems with advanced architecture capabilities.

### Focus

Designing and Scaling AI Systems

### Key Components

- Architect and deploy LLMs at scale — both open-source and commercial — with infrastructure patterns (e.g., HuggingFace Inference, vLLM, Kubernetes, GPUs)
- Optimize cost-performance tradeoffs in hosting and inferencing
- Understand AI agent security (prompt injection, sandboxing, access control)
- Design agent learning mechanisms (feedback loops, memory, retraining strategies)
