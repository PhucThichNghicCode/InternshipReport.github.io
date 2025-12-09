---
title: "Event 3"
date: "2025-11-15"
weight: 2
chapter: false
pre: " <b> 4.2. </b> "
---

# Consolidated Report: “AWS AI/ML and Generative AI Workshop”

### Event Objectives

- Gain an overview of AWS AI/ML services (Amazon SageMaker) and the Generative AI platform (Amazon Bedrock).
- Deeply understand the AIDLC (AI-Driven Development) methodology to accelerate software development.
- Master the Kiro IDE tool and new concepts like Spec-Driven Development (SDD) and Agent Hooks.
- Learn effective Prompt Engineering and Context Management for working with AI.

### Key Highlights

#### AWS AI/ML Services & Bedrock 

The program presented a comprehensive landscape of AWS AI capabilities:

- **Amazon SageMaker**: An end-to-end ML platform covering data preparation, training, fine-tuning, and integrated MLOps.
- **Amazon Bedrock**: The flagship service for Generative AI, enabling the selection of Foundation Models (Claude, Llama, Titan).
- **Advanced Features**: Discussions on RAG (Retrieval-Augmented Generation) architecture, Knowledge Base integration, Bedrock Agents for multi-step workflows, and Guardrails for content filtering.

#### AIDLC Methodology (AI-Driven Development) 

This was the core of the workflow transformation, reducing development time from 2 weeks to 1.5 days:

- **Human-Centric Philosophy**: Humans are responsible for validation, decisioning, and oversight. AI is never allowed to make decisions autonomously.
- **Three-Phase Process**: Inception (Defining Requirements/User Stories), Construction (Implementing Units), and Operation (Deploy/CICD).
- **Mob Development**: Proposed a team model where multiple roles (BA, Engineer, SA, QA) work together on a single machine to validate outputs immediately.

#### Kiro IDE & Spec-Driven Development 

Exploring the next-gen AI-native IDE (similar to Coder/VS Code):

- **Spec-Driven Development (SDD)**: Starting with specification documents instead of coding. Kiro automatically generates requirement, design, and task list files.
- **Agent Hooks**: A feature for event-driven automation using natural language (e.g., automatically running tests upon saving a file).
- **Advanced Context Management**: Automatically executing AIDLC workflows via a `steering` configuration file.

#### Context Management

- **Input Optimization**: AI understands natural language context better than raw code. Code should be extracted into project summaries or domain models.
- **Session Control**: Create separate sessions for each task (Unit of Work) to avoid context overload and minimize hallucinations.

### Key Takeaways

#### Mindset for Working with AI

- **Planning is Mandatory**: When requesting AI to perform tasks, it is mandatory to ask it to create a Plan first. Iterate on the Plan until it is correct before proceeding.
- **"Do, Don't Ban" Approach**: Instead of telling AI "don't do X", give specific instructions to "do Y". Negative constraints are generally less effective than affirmative ones.
- **The New Developer Role**: Shifting from a "coder" to a supervisor, responsible for validation and decision-making.

#### Prompting Techniques

- **Define Persona**: Always clearly define the AI's role from the beginning.
- **Clear Input/Output**: Specify the exact output format (e.g., Markdown file) instead of letting AI store it in temporary memory.

### Application

- **Adopt AIDLC Process**: Start breaking projects down into Inception, Construction, and Operation phases, and practice generating Plans before coding.
- **Switch IDE Tools**: Experiment with Kiro IDE or similar AI-assisted tools, utilizing Agent Hooks to automate testing workflows.
- **Optimize Context**: Build a habit of writing architectural summaries and domain models to feed context to AI instead of dumping the entire source code.
- **Practice Mob Development**: Organize focused team sessions to validate AI-generated results instantly.

### Event Experience

Attending the **"AWS AI/ML and Generative AI"** Workshop provided a completely fresh perspective on the future of programming. Memorable experiences included:

#### Shift in Speed and Mindset
- I was truly impressed by the **AIDLC** concept, especially its ability to drastically shorten development time. It is not just a faster tool, but a completely different approach to problem-solving.

#### The "Self-Driving Car" Analogy
- The illustration comparing software development to **controlling a self-driving car** was profound. I realized I don't need to drive every meter, but I must hold the map (Plan) and be ready to brake (Validation) to ensure safety.

#### The Power of Amazon Bedrock
- The demo on **Bedrock Agents** and **RAG** showed immense potential for building intelligent applications capable of reasoning and retrieving enterprise data, going far beyond simple chatbots.

#### Approach to Kiro IDE
- Learning about **Spec-Driven Development (SDD)** in Kiro, while noted as somewhat rigid for large projects, opened up a great direction for rapid prototyping and minimizing errors right from the design phase.