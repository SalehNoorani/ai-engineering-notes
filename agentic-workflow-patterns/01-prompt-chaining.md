# Prompt Chaining Pattern

> **Key Takeaway:** Prompt Chaining decomposes a complex task into a sequence of discrete, synchronous LLM calls, where the output of one step becomes the input for the next. This shifts the design from "autonomous agents" to "deterministic workflows."

---

## What Is It?

Prompt Chaining is an agentic workflow pattern that breaks down a multi-step task into a series of smaller, sequential LLM invocations. Instead of asking a single model to generate a complex output in one pass (which increases error rates and hallucinations), each step handles a specific sub-task with specialized instructions.

---

## Why Is It Useful?

* **Granular Control:** Allows developers to inspect, test, and optimize individual steps in the pipeline.
* **Reduced Cognitive Load:** Smaller tasks allow the LLM to focus on one narrow context, drastically improving accuracy.
* **Deterministic Paths:** It enforces structural consistency, making it ideal for standard business workflows rather than autonomous exploration.
* **Programmatic Interventions:** Traditional code (validation, data parsing, or regex) can be inserted between links in the chain.

---

## Basic Workflow

1. **Step 1 (Input Processing):** The system receives the raw user request and processes it using the first specific prompt.
2. **Transformation & Validation:** The output of Step 1 is optionally parsed or validated by code.
3. **Step 2 (Context Enrichment):** The structured output from Step 1 is embedded into the prompt template for the next LLM call.
4. **Final Generation:** The last LLM in the chain compiles the finalized result for the user.

---

## Example Use Cases

* **Automated Article Writing:**
  * Step 1: Generate an outline based on a topic.
  * Step 2: Research facts for each section of the outline.
  * Step 3: Write the full paragraphs based on the research.
* **Code Generation and Documentation:**
  * Step 1: Write a Python function based on requirements.
  * Step 2: Generate unit tests for that function.
  * Step 3: Write technical documentation for the final codebase.
* **Customer Support Automation:**
  * Step 1: Classify user intent and sentiment.
  * Step 2: Extract relevant keywords or entity IDs.
  * Step 3: Draft a response using specific business rules.

---

## Advantages

* Higher reliability and consistency across runs.
* Easier debugging (you can pinpoint exactly which prompt in the chain failed).
* Low latency compared to iterative loops (like the Reflection pattern).

---

## Limitations

* **Error Propagation:** If an early step in the chain produces bad data or hallucinates, that error compounds throughout the rest of the workflow.
* **Rigidity:** It cannot dynamically adapt to unexpected user inputs that fall outside the predefined programmatic path.

---

## Example Prompt Structure (Conceptual)

### Prompt 1: Goal Extraction
```text
Analyze the following customer email and extract the primary product they are asking about and the core issue.
Return ONLY a JSON object with keys: "product" and "issue".

Customer Email: {{customer_email}}
```
### Prompt 2: Resolution Drafting (Fed with Output 1)

```text
You are a technical support agent. Use the extracted information below to draft a polite response email. 
Refer to the resolution steps provided in our manual for this specific issue.

Product: {{product}}
Issue: {{issue}}
```
---
## Related Concepts
* Routing: Dynamically deciding which chain or specific path to execute based on an initial classification step.

* Parallelization: Running multiple sub-steps of a chain simultaneously before aggregating them.

* Orchestrator-Workers: A more advanced architecture where a central LLM dynamically divides tasks instead of following a fixed chain.