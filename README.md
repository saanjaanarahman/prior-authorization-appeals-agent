Prior Authorization & Insurance Appeals Advocate
Overview

The Prior Authorization & Insurance Appeals Advocate is an agentic AI decision-support system designed to assist healthcare administrators, medical billers, and clinicians with reviewing prior authorization denials and preparing insurance appeals.

The system evaluates insurance policy requirements against patient documentation, considers multiple possible reasoning pathways, determines whether an appeal is supported, and generates an evidence-grounded appeal draft when appropriate.

The capstone demonstrates how Retrieval-Augmented Generation (RAG), Tree-of-Thought reasoning, multi-agent coordination, guardrails, human-in-the-loop escalation, and MCP tooling can be combined into an end-to-end healthcare administrative AI workflow.

Important: This project is a demonstration using synthetic patient data. It does not provide clinical advice and does not autonomously submit insurance appeals.
Problem

Prior authorization denials can require healthcare staff to manually review multiple sources of information, including:

insurance policy requirements;
denial rationale;
patient medical documentation;
previous treatments;
procedure information; and
appeal requirements.

This process can be time-consuming and requires careful comparison between policy criteria and the evidence contained in the patient's record.

The project explores how an agentic AI system could assist with this process while maintaining evidence grounding and human oversight.

System Workflow

The system follows the following workflow:

Case Input
    ↓
Input Guardrail
    ↓
Evidence Retrieval (RAG)
    ↓
Evidence Sufficiency Guardrail
    ↓
Thought Generator
    ↓
Multiple Appeal Pathways
    ↓
Critic Agent
    ↓
Risk Guardrail
    ↓
Controller / Decision Agent
    ↓
Appeal Supported?
    ↓
Appeal Writer
    ↓
Grounding Verifier
    ↓
PASS ───────────────→ Final Draft
 │
 FAIL
 ↓
One Revision
 ↓
Verification
 ↓
Unresolved?
 ↓
Human Review


Core Components
Retrieval-Augmented Generation (RAG)

Insurance policy and patient information are chunked and converted into embeddings. Semantic retrieval identifies evidence relevant to the authorization question.

Retrieval is filtered by case ID to reduce the risk of information from one synthetic patient being used for another patient.

Tree-of-Thought Reasoning

Instead of immediately making a decision, the Thought Generator produces several plausible reasoning pathways.

For example:

standard policy requirements are satisfied;
an exception to the normal criteria may apply;
documentation is incomplete; or
the denial may be justified.
Critic Agent

The Critic evaluates each pathway against the retrieved policy and patient evidence.

Each pathway receives a score based on its evidentiary support, contradictions, and missing information.

Beam Selection

The strongest reasoning pathways survive while weaker pathways are discarded, limiting unnecessary reasoning branches.

Controller Agent

The Controller reviews the surviving pathways and determines one of four outcomes:

APPEAL_SUPPORTED
APPEAL_NOT_SUPPORTED
MORE_EVIDENCE_NEEDED
HUMAN_REVIEW
Appeal Writer and Verifier

When an appeal is supported, the system generates a professional appeal draft using only the retrieved evidence.

A separate verification step checks the draft for:

fabricated facts;
unsupported statements;
contradictions;
incorrect policy statements; and
incorrect patient information.

If verification fails, the system allows one bounded revision before escalating the case.

Guardrail Architecture

The system implements guardrails at three stages.

Pre-generation

Checks that required case information and evidence are available before substantive reasoning begins.

During-generation

Evaluates the strength of the reasoning pathways and identifies cases where the reasoning is insufficiently supported.

Post-generation

Verifies the generated appeal against the retrieved source evidence.

Cases that cannot be resolved safely can be routed to human review.

Multi-Agent Architecture

The system separates responsibilities across five functional agents:

Agent	Responsibility
Denial & Evidence Agent	Retrieves and organizes relevant case and policy evidence
Thought Generator	Generates alternative appeal reasoning pathways
Critic Agent	Evaluates and scores the proposed pathways
Controller	Selects the strongest reasoning and determines the outcome
Appeal Writer / Verifier	Drafts and validates the appeal

LangGraph coordinates the workflow and maintains shared state between components.

MCP Integration

The completed workflow is also exposed as an MCP tool, allowing another compatible AI host to invoke the prior authorization workflow as a capability.

Conceptually:

AI Host
   ↓
MCP Tool
   ↓
Prior Authorization Agent
   ↓
LangGraph Workflow

This separates the underlying agentic workflow from the interface through which it is accessed.

Web Application

A lightweight Gradio interface provides an interactive demonstration of the system.

Users can select a synthetic case and run the analysis through a simple dashboard displaying:

final decision;
risk assessment;
selected reasoning pathways;
generated appeal;
verification result; and
human-review status.

The web interface is a presentation layer over the existing agent rather than a separate decision-making system.

Technologies Used
Python
OpenAI API
LangGraph
LangChain text splitting
Sentence Transformers
semantic embeddings
cosine similarity
Tree-of-Thought reasoning
multi-agent orchestration
FastMCP / MCP
Gradio
Google Colab

## Demo

### Case 1 — Appeal Supported
The system identifies that the documented conservative treatment satisfies the policy requirement and determines that an appeal is supported.

![Appeal Supported](john-appeal-supported.png)

### Case 2 — Appeal Not Supported
The system identifies that the documented treatment duration does not satisfy the policy requirement and correctly prevents an unsupported appeal from being drafted.

![Appeal Not Supported](jane-appeal-not-supported.png)
Current Limitations

This is a capstone prototype rather than a production healthcare application. The current implementation uses a small synthetic dataset and demonstration thresholds.

A production implementation would require substantially broader evaluation, validated risk and confidence thresholds, policy version/effective-date controls, secure handling of protected health information, access controls, auditability, and integration with healthcare administrative systems.
Future Development

Potential extensions include:

larger policy and case repositories;
explicit policy version and effective-date validation;
automated document ingestion;
stronger evidence citation and provenance;
broader evaluation datasets;
calibrated confidence/risk thresholds;
production-grade observability;
secure healthcare-system integration; and
structured human-review workflows.
